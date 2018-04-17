---
title: Wbudowanych ról dla kontroli dostępu opartej na rolach na platformie Azure (RBAC) | Dokumentacja firmy Microsoft
description: W tym temacie opisano wbudowanych ról dla kontroli dostępu opartej na rolach (RBAC). Role są stale dodawane, więc sprawdzaj świeżości dokumentacji.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: ''
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 03/06/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: it-pro
ms.openlocfilehash: 3b689c6028470021734ad78dd88748f4079c383f
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Wbudowanych ról dla kontroli dostępu opartej na rolach na platformie Azure
Azure opartej na rolach kontroli dostępu (RBAC) zawiera następujące role wbudowane przypisane do użytkowników, grup i usług. Nie można zmodyfikować definicje ról wbudowanych. Można jednak utworzyć [niestandardowych ról w Azure RBAC](custom-roles.md) do określonych potrzeb organizacji.

## <a name="built-in-role-descriptions"></a>Opisy ról wbudowanych
Poniższa tabela zawiera krótkie opisy wbudowane role. Kliknij nazwę roli, aby wyświetlić szczegółowy wykaz **akcje** i **notactions** dla roli. **Akcje** właściwość określa akcje dozwolone dla zasobów platformy Azure. Ciągi akcji można używać symboli wieloznacznych. **Notactions** właściwość określa akcje, które są wykluczone z dozwolonych akcji.

Akcja definiuje, jakiego typu operacje można wykonywać na typ zasobu. Na przykład:
- **Zapis** umożliwia wykonywanie operacji PUT, POST, poprawki i DELETE.
- **Odczyt** umożliwia wykonywanie operacji GET.

W tym artykule opisano tylko różne role, które istnieją już dzisiaj. Po przypisaniu roli na użytkownika, można ograniczyć dalsze akcje dozwolone przez określenie zakresu. Jest to przydatne, jeśli mają być ktoś współautora witryny sieci Web, ale tylko dla jednej grupy zasobów.

> [!NOTE]
> Stale ewoluuje definicje ról platformy Azure. W tym artykule jest aktualizowane tak jak to możliwe, ale zawsze można znaleźć najnowsze definicje ról w programie Azure PowerShell. Użyj [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) polecenia cmdlet, aby wyświetlić listę wszystkich bieżących ról. Użytkownik może zajrzyj dostęp do konkretnej roli za pomocą `(get-azurermroledefinition "<role name>").actions` lub `(get-azurermroledefinition "<role name>").notactions` ma zastosowanie. Użyj [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) do operacji listy dostawców określonych zasobów platformy Azure.


| Wbudowane roli | Opis |
| --- | --- |
| [Właściciel](#owner) | Umożliwia zarządzanie wszystkim, w tym dostępem do zasobów. |
| [Współautora](#contributor) | Umożliwia zarządzanie wszystkim z wyjątkiem dostępu do zasobów. |
| [Czytnik](#reader) | Umożliwia wyświetlanie wszystkiego, ale nie umożliwia wprowadzania jakichkolwiek zmian. |
| [Interfejs API zarządzania usługi współautora](#api-management-service-contributor) | Może zarządzać usługą i interfejsami API |
| [Rola operatora usługi zarządzania interfejsu API](#api-management-service-operator-role) | Może zarządzać usługą, lecz nie interfejsami API |
| [Interfejs API zarządzania usługi czytnika roli](#api-management-service-reader-role) | Dostęp tylko do odczytu do usługi i interfejsów API |
| [Application Insights składnika współautora](#application-insights-component-contributor) | Umożliwia zarządzanie składnikami usługi Application Insights |
| [Application Insights migawki debugera](#application-insights-snapshot-debugger) | Nadaje użytkownikowi uprawnienie do korzystania z funkcji debugera migawek usługi Application Insights |
| [Operator zadania automatyzacji](#automation-job-operator) | Twórz zadania i zarządzaj nimi za pomocą elementów runbook usługi Automation. |
| [Operator automatyzacji](#automation-operator) | Operatorzy automatyzacji mogą uruchamiać, zatrzymywać, wstrzymywać i wznawiać zadania |
| [Operator elementów Runbook automatyzacji](#automation-runbook-operator) | Odczytuj właściwości elementu runbook, aby móc tworzyć zadania tego elementu. |
| [Właściciel rejestracji Azure stosu](#azure-stack-registration-owner) | Umożliwia zarządzanie rejestracjami w usłudze Azure Stack. |
| [Współautor kopii zapasowej](#backup-contributor) | Umożliwia zarządzanie usługami kopii zapasowych z wyjątkiem tworzenia magazynów i przyznawania dostępu innym osobom |
| [Operator kopii zapasowych](#backup-operator) | Umożliwia zarządzanie usługami kopii zapasowych z wyjątkiem usuwania kopii zapasowych, tworzenia magazynów i przyznawania dostępu innym osobom |
| [Czytnik kopii zapasowej](#backup-reader) | Może wyświetlać usługi kopii zapasowych, ale nie może wprowadzać zmian |
| [Czytnik rozliczeń](#billing-reader) | Umożliwia dostęp do odczytu do danych dotyczących rozliczeń |
| [BizTalk Contributor](#biztalk-contributor) | Umożliwia zarządzanie usługami BizTalk Services, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor punktu końcowego CDN](#cdn-endpoint-contributor) | Może zarządzać punktami końcowymi usługi CDN, lecz nie może przyznawać dostępu innym użytkownikom. |
| [Czytnik punktu końcowego CDN](#cdn-endpoint-reader) | Może wyświetlać punkty końcowe usługi CDN, ale nie może wprowadzać zmian. |
| [Współautor profilu CDN](#cdn-profile-contributor) | Może zarządzać profilami usługi CDN i ich punktami końcowymi, ale nie może przyznawać dostępu innym użytkownikom. |
| [Czytnik profilu CDN](#cdn-profile-reader) | Może wyświetlać profile usługi CDN i ich punkty końcowe, ale nie może wprowadzać zmian. |
| [Współautor klasycznej sieci](#classic-network-contributor) | Umożliwia zarządzanie klasycznymi sieciami, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor konta magazynu Classic](#classic-storage-account-contributor) | Umożliwia zarządzanie kontami klasycznego magazynu, ale nie dostęp do nich. |
| [Rola usługi Operator klucza konta magazynu klasycznego](#classic-storage-account-key-operator-service-role) | Operatorzy kluczy klasycznych kont magazynu mogą wyświetlać listę kluczy dla klasycznych kont magazynu i ponownie je generować |
| [Współautor klasyczne maszyny wirtualnej](#classic-virtual-machine-contributor) | Umożliwia zarządzanie klasycznych maszyn wirtualnych, ale nie do nich dostępu i nie sieci wirtualnej lub konta magazynu, które są połączone. |
| [Współautor ClearDB MySQL bazy danych](#cleardb-mysql-db-contributor) | Umożliwia zarządzanie bazami danych ClearDB MySQL, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Rozwiązania cosmos bazy danych konta czytnik roli](#cosmos-db-account-reader-role) | Można odczytać danych konta bazy danych Azure rozwiązania Cosmos. Zobacz [współautora konta usługi DocumentDB](#documentdb-account-contributor) do zarządzania kontami bazy danych Azure rozwiązania Cosmos. |
| [Współautor fabryki danych](#data-factory-contributor) | Tworzenie fabryk danych i zawartych w nich zasobów podrzędnych oraz zarządzanie nimi. |
| [Data Lake Analytics Developer](#data-lake-analytics-developer) | Umożliwia przesyłanie własnych zadań, monitorowanie ich i zarządzanie nimi, ale nie tworzenie ani usuwanie kont usługi Data Lake Analytics. |
| [DevTest Labs użytkownika](#devtest-labs-user) | Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w usłudze Azure DevTest Labs. |
| [Współautor strefy DNS](#dns-zone-contributor) | Umożliwia zarządzanie strefami DNS i zestawami rekordów w usłudze Azure DNS, ale nie zapewnia kontroli dostępu do nich. |
| [Współautor konta usługi DocumentDB](#documentdb-account-contributor) | Można zarządzać kontami bazy danych Azure rozwiązania Cosmos. Azure DB rozwiązania Cosmos jest znanego wcześniej jako usługi DocumentDB. |
| [Współautor konta systemów inteligentnych](#intelligent-systems-account-contributor) | Umożliwia zarządzanie kontami usługi Intelligent Systems, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor magazyn kluczy](#key-vault-contributor) | Umożliwia zarządzanie magazynami kluczy, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Twórca laboratorium](#lab-creator) | Umożliwia tworzenie, zarządzania, Usuń z zarządzanego laboratoriów w ramach konta Azure laboratorium. |
| [Współautor analizy dzienników](#log-analytics-contributor) | Współautor analizy dziennika można odczytać wszystkich danych monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia maszyny Wirtualnej do maszyn wirtualnych; Odczyt kluczy konta magazynu, aby można było skonfigurować kolekcję dzienników z usługi Magazyn Azure; Tworzenie i konfigurowanie kont automatyzacji; Dodawanie rozwiązań. i konfigurowania diagnostyki Azure dla wszystkich zasobów systemu Azure. |
| [Czytnik dziennika analityka](#log-analytics-reader) | Czytelnik usługi Log Analytics może wyświetlać i wyszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym konfigurację diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
| [Współautor aplikacji logiki](#logic-app-contributor) | Umożliwia zarządzanie aplikacjami logiki, lecz nie dostęp do nich. |
| [Operator aplikacji logiki](#logic-app-operator) | Umożliwia odczytywanie, włączanie i wyłączanie aplikacji logiki. |
| [Współautor tożsamości zarządzanych](#managed-identity-contributor) | Tworzenia, odczytu, aktualizacji i usuwania tożsamości przypisane przez użytkownika |
| [Zarządzane tożsamość — Operator](#managed-identity-operator) | Przeczytaj i przypisz przypisać tożsamość użytkownika |
| [Monitorowanie współautora](#monitoring-contributor) | Można odczytać wszystkich danych monitorowania i edytować ustawienia monitorowania. Zobacz też [Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń z monitorem Azure](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Czytnik monitorowania](#monitoring-reader) | Może czytać wszystkie dane monitorowania (metryki, dzienniki itp.). Zobacz też [Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń z monitorem Azure](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Współautor sieci](#network-contributor) | Umożliwia zarządzanie sieciami, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Nowy współtwórca konta APM Relic](#new-relic-apm-account-contributor) | Umożliwia zarządzanie kontami i aplikacjami usługi New Relic Application Performance Management, ale nie umożliwia uzyskiwania dostępu do nich. |
| [Współautor pamięci podręcznej redis](#redis-cache-contributor) | Umożliwia zarządzanie pamięciami podręcznymi Redis, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Harmonogram zadania kolekcje współautora](#scheduler-job-collections-contributor) | Umożliwia zarządzanie kolekcjami zadań usługi Scheduler, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor usługi wyszukiwania](#search-service-contributor) | Umożliwia zarządzanie usługami Search, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Administrator zabezpieczeń](#security-admin) | W Centrum zabezpieczeń tylko: można wyświetlić zasady zabezpieczeń, wyświetlanie stanów zabezpieczeń, Edytuj zasady zabezpieczeń, wyświetlanie alertów i zalecenia, odrzucać alerty i zalecenia |
| [Menedżer zabezpieczeń](#security-manager) | Umożliwia zarządzanie składnikami zabezpieczeń, zasadami zabezpieczeń i maszynami wirtualnymi |
| [Czytnik zabezpieczeń](#security-reader) | W Centrum zabezpieczeń tylko: można wyświetlić zalecenia i alertów, widok zasady zabezpieczeń, wyświetlanie stanów zabezpieczeń, ale nie można wprowadzić zmian |
| [Współautor odzyskiwania lokacji](#site-recovery-contributor) | Pozwala zarządzać usługą Site Recovery z wyjątkiem tworzenia magazynu i przypisywania ról |
| [Operator odzyskiwania lokacji](#site-recovery-operator) | Pozwala przechodzić do trybu failover i przywracać sprawność po awarii, ale nie umożliwia wykonywania innych operacji zarządzania usługi Site Recovery |
| [Czytnik odzyskiwania lokacji](#site-recovery-reader) | Pozwala wyświetlać stan usługi Site Recovery, ale nie umożliwia wykonywania innych operacji zarządzania |
| [Współautor bazy danych SQL](#sql-db-contributor) | Umożliwia zarządzanie baz danych, ale nie do nich dostępu. Ponadto nie można zarządzać ich zasad związanych z zabezpieczeniami lub ich nadrzędnego serwerów SQL. |
| [SQL Security Manager](#sql-security-manager) | Umożliwia zarządzanie zasadami serwerów i baz danych SQL związanymi z zabezpieczeniami, ale nie umożliwia uzyskiwania do nich dostępu. |
| [SQL Server współautora](#sql-server-contributor) | Umożliwia zarządzanie serwerami i bazami danych SQL, ale nie umożliwia uzyskiwania dostępu do nich ani do ich zasad związanych z zabezpieczeniami. |
| [Współautor konta magazynu](#storage-account-contributor) | Umożliwia zarządzanie kontami magazynu, ale nie dostęp do nich. |
| [Rola usługi Operator klucza konta magazynu](#storage-account-key-operator-service-role) | Operatorzy kluczy kont magazynu mogą wyświetlać listę kluczy dla kont magazynu i ponownie je generować |
| [Współautor żądania obsługi](#support-request-contributor) | Umożliwia tworzenie żądań pomocy technicznej i zarządzanie nimi |
| [Współautor Menedżera ruchu](#traffic-manager-contributor) | Umożliwia zarządzanie profilami usługi Traffic Manager, ale nie zapewnia kontroli dostępu do nich. |
| [Administrator dostępu użytkowników](#user-access-administrator) | Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure. |
| [Identyfikator logowania administratora maszyny wirtualnej](#virtual-machine-administrator-login) | — Użytkownicy z tą rolą mieć możliwość logowania do maszyny wirtualnej z administratorem systemu Windows lub Linux głównego użytkownikowi uprawnienia. |
| [Współautor maszyny wirtualnej](#virtual-machine-contributor) | Umożliwia zarządzanie maszyn wirtualnych, ale nie do nich dostępu i nie sieci wirtualnej lub konta magazynu, które są połączone. |
| [Dane logowania użytkownika maszyny wirtualnej](#virtual-machine-user-login) | Użytkownicy z tą rolą mieć możliwość logowania do maszyny wirtualnej przez użytkowników. |
| [Współautor Plan sieci Web](#web-plan-contributor) | Umożliwia zarządzanie planami sieci Web dla witryn sieci Web, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor witryny sieci Web](#website-contributor) | Umożliwia zarządzanie witrynami sieci Web (nie planami sieci Web), ale nie umożliwia uzyskiwania do nich dostępu. |

W poniższych tabelach opisano określone uprawnienia do poszczególnych ról. Może to obejmować **akcje**, które dają uprawnienia, i **NotActions**, które ograniczają.

## <a name="owner"></a>Właściciel
Umożliwia zarządzanie wszystkim, w tym dostępem do zasobów.

| **Akcje** |  |
| --- | --- |
| * | Tworzenie i zarządzanie zasobami wszystkich typów |

## <a name="contributor"></a>Współautor
Umożliwia zarządzanie wszystkim z wyjątkiem dostępu do zasobów.

| **Akcje** |  |
| --- | --- |
| * | Tworzenie i zarządzanie zasobami wszystkich typów |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete | Nie można usuwać role i przypisania ról |
| Microsoft.Authorization/*/Write | Nie można utworzyć role i przypisania ról |
| Microsoft.Authorization/elevateAccess/Action | Przyznaje osobie wywołującej uprawnienia administratora dostępu użytkowników w zakresie dzierżawy |

## <a name="reader"></a>Czytelnik
Umożliwia wyświetlanie wszystkiego, ale nie umożliwia wprowadzania jakichkolwiek zmian.

| **Akcje** |  |
| --- | --- |
| * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |

## <a name="api-management-service-contributor"></a>Współautor usługi API Management
Może zarządzać usługą i interfejsami API

| **Akcje** |  |
| --- | --- |
| Microsoft.ApiManagement/service/* | Tworzenie i zarządzanie nimi usługi Zarządzanie interfejsami API |
| Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="api-management-service-operator-role"></a>Rola Operator usługi API Management
Może zarządzać usługą, lecz nie interfejsami API

| **Akcje** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Wystąpienie usługi zarządzania interfejsu API odczytu |
| Microsoft.ApiManagement/service/backup/action | Usługi zarządzania interfejsu API tworzenia kopii zapasowej do określonego kontenera użytkownika podane konto magazynu |
| Microsoft.ApiManagement/service/delete | Usuń wystąpienia usługi interfejsu API zarządzania |
| Microsoft.ApiManagement/service/managedeployments/action | Zmień jednostki SKU/jednostki, dodawania i usuwania wdrożenia regionalnych interfejsu API usługi zarządzania |
| Microsoft.ApiManagement/service/read | Odczytać metadanych dla wystąpienia interfejsu API usługi zarządzania |
| Microsoft.ApiManagement/service/restore/action | Przywracanie usługi interfejsu API zarządzania z kontenera określonej w użytkownika podane konto magazynu |
| Microsoft.ApiManagement/service/updatecertificate/action | Przekaż certyfikat protokołu SSL dla interfejsu API usługi zarządzania |
| Microsoft.ApiManagement/service/updatehostname/action | Konfiguracja, zaktualizować lub usunąć nazwy domeny niestandardowej dla usługi interfejsu API zarządzania |
| Microsoft.ApiManagement/service/write | Utwórz nowe wystąpienie interfejsu API usługi zarządzania |
| Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Pobierz listę kluczy użytkownika |

## <a name="api-management-service-reader-role"></a>Rola Czytnik usługi API Management
Dostęp tylko do odczytu do usługi i interfejsów API

| **Akcje** |  |
| --- | --- |
| Microsoft.ApiManagement/service/*/read | Wystąpienie usługi zarządzania interfejsu API odczytu |
| Microsoft.ApiManagement/service/read | Odczytać metadanych dla wystąpienia interfejsu API usługi zarządzania |
| Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

| **NotActions** |  |
| --- | --- |
| Microsoft.ApiManagement/service/users/keys/read | Pobierz listę kluczy użytkownika |

## <a name="application-insights-component-contributor"></a>Współautor składników usługi Application Insights
Umożliwia zarządzanie składnikami usługi Application Insights

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.Insights/components/* | Tworzenie i zarządzanie nimi składniki Insights |
| Microsoft.Insights/webtests/* | Tworzenie i zarządzanie nimi testy sieci web |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="application-insights-snapshot-debugger"></a>Debuger migawek usługi Application Insights
Nadaje użytkownikowi uprawnienie do korzystania z funkcji debugera migawek usługi Application Insights

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Insights/components/*/read |  |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="automation-job-operator"></a>Operator zadań usługi Automation
Twórz zadania i zarządzaj nimi za pomocą elementów runbook usługi Automation.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Automation/automationAccounts/jobs/read | Pobiera zadanie usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Wznawia zadanie usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Zatrzymuje zadanie usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadań usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Wstrzymuje zadanie usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/jobs/write | Tworzy zadanie usługi Automatyzacja Azure |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="automation-operator"></a>Operator usługi
Operatorzy automatyzacji mogą uruchamiać, zatrzymywać, wstrzymywać i wznawiać zadania

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Automation/automationAccounts/jobs/read | Pobiera zadanie usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/jobs/resume/action | Wznawia zadanie usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/jobs/stop/action | Zatrzymuje zadanie usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadań usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/jobs/suspend/action | Wstrzymuje zadanie usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/jobs/write | Tworzy zadanie usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/jobSchedules/read | Pobiera harmonogram zadań usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/jobSchedules/write | Tworzy harmonogram zadań usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/linkedWorkspace/read | Pobiera obszar roboczy połączony z kontem automatyzacji |
| Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
| Microsoft.Automation/automationAccounts/read | Pobiera konto usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/runbooks/read | Pobiera element runbook usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/schedules/read | Pobiera zasób harmonogramu usługi Automatyzacja Azure |
| Microsoft.Automation/automationAccounts/schedules/write | Tworzy lub aktualizuje zasób harmonogramu usługi Automatyzacja Azure |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="automation-runbook-operator"></a>Operator elementów runbook usługi Automation
Odczytuj właściwości elementu runbook, aby móc tworzyć zadania tego elementu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Automation/automationAccounts/runbooks/read | Pobiera element runbook usługi Automatyzacja Azure |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="azure-stack-registration-owner"></a>Właściciel rejestracji w usłudze Azure Stack
Umożliwia zarządzanie rejestracjami w usłudze Azure Stack.

| **Akcje** |  |
| --- | --- |
| Microsoft.AzureStack/registrations/products/listDetails/action | Pobiera rozszerzony szczegóły produktu Azure Marketplace stosu |
| Microsoft.AzureStack/registrations/products/read | Pobiera właściwości produktu Azure Marketplace stosu |
| Microsoft.AzureStack/registrations/read | Pobiera właściwości rejestracji Azure stosu |

## <a name="backup-contributor"></a>Współautor kopii zapasowych
Umożliwia zarządzanie usługami kopii zapasowych z wyjątkiem tworzenia magazynów i przyznawania dostępu innym osobom

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Zarządzanie wyniki operacji tworzenia kopii zapasowej zarządzania |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Tworzenie i zarządzanie nimi kopii zapasowej kontenery w kopii zapasowej sieci szkieletowej magazynu usług odzyskiwania |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Tworzenie i zarządzanie nimi zadania tworzenia kopii zapasowej |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportowanie zadań |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Tworzenie i zarządzanie nimi metadane związane z zarządzaniem kopii zapasowej |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Utwórz i Zarządzaj wynikami operacji zarządzania kopiami zapasowymi |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | Tworzenie i zarządzanie zasadami tworzenia kopii zapasowej |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Tworzenie i zarządzanie nimi elementy, które można utworzyć kopię zapasową |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Tworzenie i zarządzanie nimi kopii zapasowej elementów |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Tworzenie i zarządzanie nimi kontenery zawierający elementy kopii zapasowej |
| Microsoft.RecoveryServices/Vaults/certificates/* | Tworzenie i zarządzanie certyfikatami związane z kopii zapasowej w magazynie usług odzyskiwania |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Tworzenie i zarządzanie nimi rozszerzone informacje związane z magazynu |
| Microsoft.RecoveryServices/Vaults/read | Operacja Get magazynu pobiera obiekt reprezentujący zasobów platformy Azure typu "magazynu" |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Zarządzanie pobieranie operacji wykrywania nowo utworzony kontenerów |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Tworzenie i Zarządzanie tożsamościami w zarejestrowany |
| Microsoft.RecoveryServices/Vaults/usages/* | Tworzenie i zarządzanie użyciem magazynu usług odzyskiwania |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dla chronionych serwerów i chronione elementy usług odzyskiwania. |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alertów dla magazynu usług odzyskiwania. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Zwraca wynik operacji eksportowania zadania. |
| Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="backup-operator"></a>Operator kopii zapasowych
Umożliwia zarządzanie usługami kopii zapasowych z wyjątkiem usuwania kopii zapasowych, tworzenia magazynów i przyznawania dostępu innym osobom

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/tworzenia kopii zapasowej/działania | Wykonuje kopię zapasową elementu chronionego. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Pobiera wynik operacji wykonanej na elementach chronionych. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Zwraca stan operacji wykonanej na elementach chronionych. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Zwraca szczegóły obiektu elemencie chroniony |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/Odczyt | Pobierz punkty odzyskiwania dla elementów chronionych. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints / / Akcja przywracania | Przywróć punkty odzyskiwania dla elementów chronionych. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Tworzenie kopii zapasowej elementu chronione |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkich zarejestrowanych kontenerów |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | Tworzenie i zarządzanie nimi zadania tworzenia kopii zapasowej |
| Microsoft.RecoveryServices/Vaults/backupJobs/cancel/action | Anulowanie zadania |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Zwraca wynik operacji zadania. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Zwraca wszystkie obiekty zadania |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportowanie zadań |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Zwraca metadane zarządzania kopiami zapasowymi magazynu usług Recovery Services. |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Utwórz i Zarządzaj wynikami operacji zarządzania kopiami zapasowymi |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Tworzenie i zarządzanie nimi elementy, które można utworzyć kopię zapasową |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/read | Zwraca listę wszystkich elementów podlegających ochronie |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należących do subskrypcji |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dla chronionych serwerów i chronione elementy usług odzyskiwania. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
| Microsoft.RecoveryServices/Vaults/read | Operacja Get magazynu pobiera obiekt reprezentujący zasobów platformy Azure typu "magazynu" |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | Zarządzanie pobieranie operacji wykrywania nowo utworzony kontenerów |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Wyniki operacji Get operacji można używać Pobierz stan operacji i wynik operacji asynchronicznie przesłane |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Kontenery pobrać operacji można używać Pobierz kontenery zarejestrowanych dla każdego zasobu. |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operacji zarejestrować kontenera usług można używać do rejestrowania kontener z usługą odzyskiwania. |
| Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Odzyskiwanie elementów błyskawicznych udostępniania dla chronionego elementu |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odwołanie elementu błyskawicznych odzyskiwania dla chronionego elementu |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alertów dla magazynu usług odzyskiwania. |
| Microsoft.RecoveryServices/Vaults/storageConfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/* |  |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Zwraca wynik operacji eksportowania zadania. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationStatus/read |  |
| Microsoft.RecoveryServices/Vaults/certificates/write | Operacja aktualizacji zasobów certyfikatu aktualizuje certyfikat poświadczeń zasobów/magazynu. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="backup-reader"></a>Czytelnik kopii zapasowych
Może wyświetlać usługi kopii zapasowych, ale nie może wprowadzać zmian

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ operationResults/read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationResults/read | Pobiera wynik operacji wykonanej na elementach chronionych. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/operationsStatus/read | Zwraca stan operacji wykonanej na elementach chronionych. |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Zwraca szczegóły obiektu elemencie chroniony |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkich zarejestrowanych kontenerów |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Zwraca wynik operacji zadania. |
| Microsoft.RecoveryServices/Vaults/backupJobs/read | Zwraca wszystkie obiekty zadania |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportowanie zadań |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Zwraca metadane zarządzania kopiami zapasowymi magazynu usług Recovery Services. |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Zwraca wynik operacji tworzenia kopii zapasowej magazynu usług Recovery Services. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należących do subskrypcji |
| Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dla chronionych serwerów i chronione elementy usług odzyskiwania. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
| Microsoft.RecoveryServices/Vaults/read | Operacja Get magazynu pobiera obiekt reprezentujący zasobów platformy Azure typu "magazynu" |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Wyniki operacji Get operacji można używać Pobierz stan operacji i wynik operacji asynchronicznie przesłane |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Kontenery pobrać operacji można używać Pobierz kontenery zarejestrowanych dla każdego zasobu. |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alertów dla magazynu usług odzyskiwania. |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupconfig/vaultconfig/read |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/recoveryPoints/Odczyt | Pobierz punkty odzyskiwania dla elementów chronionych. |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Zwraca wynik operacji eksportowania zadania. |
| Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |

## <a name="billing-reader"></a>Czytelnik rozliczeń
Umożliwia dostęp do odczytu do danych dotyczących rozliczeń

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Billing/*/read | Odczytywanie informacji o rozliczeń |
| Microsoft.Consumption/*/read |  |
| Microsoft.Commerce/*/read |  |
| Microsoft.Management/managementGroups/read | Wyświetlanie listy grup zarządzania dla tego uwierzytelnionego użytkownika. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="biztalk-contributor"></a>Współautor usługi BizTalk
Umożliwia zarządzanie usługami BizTalk Services, ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.BizTalkServices/BizTalk/* | Tworzenie i zarządzanie nimi usługi BizTalk services |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cdn-endpoint-contributor"></a>Współautor punktu końcowego usługi CDN
Może zarządzać punktami końcowymi usługi CDN, lecz nie może przyznawać dostępu innym użytkownikom.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/* |  |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cdn-endpoint-reader"></a>Czytelnik punktu końcowego usługi CDN
Może wyświetlać punkty końcowe usługi CDN, ale nie może wprowadzać zmian.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/endpoints/*/read |  |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cdn-profile-contributor"></a>Współautor profilu CDN
Może zarządzać profilami usługi CDN i ich punktami końcowymi, ale nie może przyznawać dostępu innym użytkownikom.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/* |  |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cdn-profile-reader"></a>Czytelnik profilu usługi CDN
Może wyświetlać profile usługi CDN i ich punkty końcowe, ale nie może wprowadzać zmian.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Cdn/edgenodes/read |  |
| Microsoft.Cdn/operationresults/* |  |
| Microsoft.Cdn/profiles/*/read |  |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="classic-network-contributor"></a>Współautor klasycznej sieci
Umożliwia zarządzanie klasycznymi sieciami, ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
| Microsoft.ClassicNetwork/* | Tworzenie i zarządzanie nimi klasycznych sieci |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="classic-storage-account-contributor"></a>Współautor klasycznego konta magazynu
Umożliwia zarządzanie kontami klasycznego magazynu, ale nie dostęp do nich.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
| Microsoft.ClassicStorage/storageAccounts/* | Tworzenie i zarządzanie nimi kont magazynu |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="classic-storage-account-key-operator-service-role"></a>Rola usługi Operator kluczy klasycznych kont magazynu
Operatorzy kluczy klasycznych kont magazynu mogą wyświetlać listę kluczy dla klasycznych kont magazynu i ponownie je generować

| **Akcje** |  |
| --- | --- |
| Microsoft.ClassicStorage/storageAccounts/listkeys/action | Wyświetla klucze dostępu dla kont magazynu. |
| Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Generuje ponownie istniejące klucze dostępu dla konta magazynu. |

## <a name="classic-virtual-machine-contributor"></a>Współautor klasycznej maszyny wirtualnej
Umożliwia zarządzanie klasycznych maszyn wirtualnych, ale nie do nich dostępu i nie sieci wirtualnej lub konta magazynu, które są połączone.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
| Microsoft.ClassicCompute/domainNames/* | Tworzenie i zarządzanie nazwami domen klasycznego obliczeń |
| Microsoft.ClassicCompute/virtualMachines/* | Tworzenie i zarządzanie maszynami wirtualnymi |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
| Microsoft.ClassicNetwork/reservedIps/link/action | Połącz zarezerwowany adres IP |
| Microsoft.ClassicNetwork/reservedIps/read | Pobiera zastrzeżone adresy IP |
| Microsoft.ClassicNetwork/virtualNetworks/join/action | Umożliwia dołączenie do sieci wirtualnej. |
| Microsoft.ClassicNetwork/virtualNetworks/read | Pobiera sieć wirtualną. |
| Microsoft.ClassicStorage/storageAccounts/disks/read | Zwraca dysk konta magazynu. |
| Microsoft.ClassicStorage/storageAccounts/images/read | Zwraca obraz konta magazynu. |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla klucze dostępu dla kont magazynu. |
| Microsoft.ClassicStorage/storageAccounts/read | Zwraca konto magazynu z podanym kontem. |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cleardb-mysql-db-contributor"></a>Współautor bazy danych MySQL ClearDB
Umożliwia zarządzanie bazami danych ClearDB MySQL, ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
| successbricks.cleardb/databases/* | Tworzenie i zarządzanie bazami danych ClearDB MySQL |

## <a name="cosmos-db-account-reader-role"></a>Rola czytelnika konta usługi Cosmos DB
Można odczytać danych konta bazy danych Azure rozwiązania Cosmos. Zobacz [współautora konta usługi DocumentDB](#documentdb-account-contributor) do zarządzania kontami bazy danych Azure rozwiązania Cosmos.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról można uprawnienia do odczytu do każdego użytkownika |
| Microsoft.DocumentDB/*/read | Przeczytaj żadnej kolekcji |
| Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Odczytuje klucze tylko do odczytu konta bazy danych. |
| Microsoft.Insights/MetricDefinitions/read | Przeczytaj definicje metryk |
| Microsoft.Insights/Metrics/read | Odczytaj metryki |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="data-factory-contributor"></a>Współautor Data Factory
Tworzenie fabryk danych i zawartych w nich zasobów podrzędnych oraz zarządzanie nimi.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania roli |
| Microsoft.DataFactory/dataFactories/* | Utwórz i Zarządzaj fabryki danych i zasoby podrzędne w nich. |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="data-lake-analytics-developer"></a>Deweloper usługi Data Lake Analytics
Umożliwia przesyłanie własnych zadań, monitorowanie ich i zarządzanie nimi, ale nie tworzenie ani usuwanie kont usługi Data Lake Analytics.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.BigAnalytics/accounts/* |  |
| Microsoft.DataLakeAnalytics/accounts/* |  |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

| **NotActions** |  |
| --- | --- |
| Microsoft.BigAnalytics/accounts/Delete |  |
| Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
| Microsoft.BigAnalytics/accounts/Write |  |
| Microsoft.DataLakeAnalytics/accounts/Delete | Usuwanie konta DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Przyznaj uprawnienia, aby anulować zadania przesłane przez innych użytkowników. |
| Microsoft.DataLakeAnalytics/accounts/Write | Utwórz lub zaktualizuj konto DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Utwórz lub zaktualizuj połączonego konta DataLakeStore DataLakeAnalytics konta. |
| Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Odłącz od konta DataLakeAnalytics przy użyciu konta DataLakeStore. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Utwórz lub zaktualizuj konto magazynu połączonego konta DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Odłączanie konta magazynu z konta DataLakeAnalytics. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
| Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Usuwanie reguły zapory. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Utwórz lub zaktualizuj zasady obliczeń. |
| Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Usuń zasady obliczeń. |

## <a name="devtest-labs-user"></a>Użytkownik usługi DevTest Labs
Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w usłudze Azure DevTest Labs.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania roli |
| Microsoft.Compute/availabilitySets/read | Pobierz właściwości zestawu dostępności |
| Microsoft.Compute/virtualMachines/*/read | Odczytywania właściwości maszyny wirtualnej (rozmiary maszyn wirtualnych stanu w czasie wykonywania, rozszerzeń maszyny Wirtualnej, itp.) |
| Microsoft.Compute/virtualMachines/deallocate/action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
| Microsoft.Compute/virtualMachines/read | Pobierz właściwości maszyny wirtualnej |
| Microsoft.Compute/virtualMachines/restart/action | Uruchamia ponownie maszynę wirtualną |
| Microsoft.Compute/virtualMachines/start/action | Uruchamia maszynę wirtualną |
| Microsoft.DevTestLab/*/read | Odczytywania właściwości laboratorium |
| Microsoft.DevTestLab/labs/createEnvironment/action | Tworzenie maszyn wirtualnych w laboratorium. |
| Microsoft.DevTestLab/labs/claimAnyVm/action | Oświadczenia losowe claimable maszynę wirtualną w laboratorium. |
| Microsoft.DevTestLab/labs/formulas/delete | Usuń formuły. |
| Microsoft.DevTestLab/labs/formulas/read | Przeczytaj formuły. |
| Microsoft.DevTestLab/labs/formulas/write | Dodaje lub modyfikuje formuły. |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Ocenia zasady laboratorium. |
| Microsoft.DevTestLab/labs/virtualMachines/claim/action | Przejmowanie na własność istniejącej maszyny wirtualnej |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Dołącza do puli adresów zaplecza modułu równoważenia obciążenia |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Tworzy sprzężenie reguły nat dla ruchu przychodzącego modułu równoważenia obciążenia |
| Microsoft.Network/networkInterfaces/*/read | Odczytywania właściwości interfejsu sieciowego (na przykład wszystkich usług równoważenia obciążenia interfejsu sieciowego jest częścią) |
| Microsoft.Network/networkInterfaces/join/action | Dołącza maszynę wirtualną do interfejsu sieciowego |
| Microsoft.Network/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
| Microsoft.Network/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
| Microsoft.Network/publicIPAddresses/*/read | Odczytywania właściwości publicznego adresu IP |
| Microsoft.Network/publicIPAddresses/join/action | Dołącza do publicznego adresu ip |
| Microsoft.Network/publicIPAddresses/read | Pobiera definicję adres publiczny adres ip. |
| Microsoft.Network/virtualNetworks/subnets/join/action | Tworzy sprzężenie sieci wirtualnej |
| Microsoft.Resources/deployments/operations/read | Pobiera operacje wdrażania lub wyświetla ich listę. |
| Microsoft.Resources/deployments/read | Pobiera wdrożenia lub wyświetla ich listę. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Storage/storageAccounts/listKeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |

| **NotActions** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/vmSizes/read | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |

## <a name="dns-zone-contributor"></a>Współautor strefy DNS
Umożliwia zarządzanie strefami DNS i zestawami rekordów w usłudze Azure DNS, ale nie zapewnia kontroli dostępu do nich.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.Network/dnsZones/* | Tworzenie i zarządzanie strefy DNS i rekordów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="documentdb-account-contributor"></a>Współautor konta bazy danych DocumentDB
Można zarządzać kontami bazy danych Azure rozwiązania Cosmos. Azure DB rozwiązania Cosmos jest znanego wcześniej jako usługi DocumentDB.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania roli |
| Microsoft.DocumentDb/databaseAccounts/* | Tworzenie i zarządzanie kontami bazy danych Azure rozwiązania Cosmos |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="intelligent-systems-account-contributor"></a>Współautor konta usługi Systemy inteligentne
Umożliwia zarządzanie kontami usługi Intelligent Systems, ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania roli |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.IntelligentSystems/accounts/* | Tworzenie i zarządzanie kontami systemów inteligentnych |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="key-vault-contributor"></a>Współautor magazynu kluczy
Umożliwia zarządzanie magazynami kluczy, ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.KeyVault/* |  |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

| **NotActions** |  |
| --- | --- |
| Microsoft.KeyVault/locations/deletedVaults/purge/action | Przeczyść usunięty nietrwale magazyn kluczy |
| Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Twórca laboratorium
Umożliwia tworzenie, zarządzania, Usuń z zarządzanego laboratoriów w ramach konta Azure laboratorium.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.ManagedLab/labAccounts/createLab/action | Tworzenie laboratorium w ramach konta laboratorium. |
| Microsoft.ManagedLab/labAccounts/*/read |  |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="log-analytics-contributor"></a>Współautor usługi Log Analytics
Współautor analizy dziennika można odczytać wszystkich danych monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia maszyny Wirtualnej do maszyn wirtualnych; Odczyt kluczy konta magazynu, aby można było skonfigurować kolekcję dzienników z usługi Magazyn Azure; Tworzenie i konfigurowanie kont automatyzacji; Dodawanie rozwiązań. i konfigurowania diagnostyki Azure dla wszystkich zasobów systemu Azure.

| **Akcje** |  |
| --- | --- |
| * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
| Microsoft.Automation/automationAccounts/* |  |
| Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla klucze dostępu dla kont magazynu. |
| Microsoft.Compute/virtualMachines/extensions/* |  |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla serwera analiz |
| Microsoft.OperationalInsights/* |  |
| Microsoft.OperationsManagement/* |  |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
| Microsoft.Storage/storageAccounts/listKeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="log-analytics-reader"></a>Czytelnik usługi Log Analytics
Czytelnik usługi Log Analytics może wyświetlać i wyszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym konfigurację diagnostyki platformy Azure dla wszystkich zasobów platformy Azure.

| **Akcje** |  |
| --- | --- |
| * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
| Microsoft.OperationalInsights/workspaces/analytics/query/action | Wyszukiwanie przy użyciu nowy aparat. |
| Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

| **NotActions** |  |
| --- | --- |
| Microsoft.OperationalInsights/workspaces/sharedKeys/read | Pobiera klucze udostępnionego dla obszaru roboczego. Klucze te są używane do połączenia agentów Microsoft Operational Insights do obszaru roboczego. |

## <a name="logic-app-contributor"></a>Współautor aplikacji logiki
Umożliwia zarządzanie aplikacjami logiki, lecz nie dostęp do nich.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla klucze dostępu dla kont magazynu. |
| Microsoft.ClassicStorage/storageAccounts/read | Zwraca konto magazynu z podanym kontem. |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienie diagnostyczne dla serwera analiz |
| Microsoft.Insights/logdefinitions/* | To uprawnienie jest wymagane dla użytkowników, którzy potrzebują dostępu do dzienników aktywności w portalu. Lista kategorii dziennika w dzienniku aktywności. |
| Microsoft.Insights/metricDefinitions/* | Odczytaj definicje metryki (listy dostępnych typów metryki dla zasobu). |
| Microsoft.Logic/* | Zarządza zasobami Logic Apps. |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/operationresults/read | Pobiera wyniki operacji subskrypcji. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Storage/storageAccounts/listkeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
| Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
| Microsoft.Web/connectionGateways/* | Tworzenie i zarządza bramy połączenia. |
| Microsoft.Web/connections/* | Tworzenie i zarządza nimi połączenia. |
| Microsoft.Web/customApis/* | Tworzy i którymi zarządza API niestandardowe. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Pobiera właściwości planu usługi App Service |
| Microsoft.Web/sites/functions/listSecrets/action | Listy kluczy tajnych funkcje aplikacji sieci Web. |

## <a name="logic-app-operator"></a>Operator aplikacji logiki
Umożliwia odczytywanie, włączanie i wyłączanie aplikacji logiki.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/*/read | Informacje na temat technologii odczytu reguły alertów |
| Microsoft.Insights/diagnosticSettings/*/read | Pobiera ustawienia diagnostyki dla aplikacji logiki |
| Microsoft.Insights/metricDefinitions/*/read | Pobiera dostępne metryki dla usługi Logic Apps. |
| Microsoft.Logic/*/read | Odczytuje zasoby Logic Apps. |
| Microsoft.Logic/workflows/disable/action | Wyłącza przepływ pracy. |
| Microsoft.Logic/workflows/enable/action | Włącza przepływ pracy. |
| Microsoft.Logic/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
| Microsoft.Resources/deployments/operations/read | Pobiera operacje wdrażania lub wyświetla ich listę. |
| Microsoft.Resources/subscriptions/operationresults/read | Pobiera wyniki operacji subskrypcji. |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
| Microsoft.Web/connectionGateways/*/read | Przeczytaj połączenia bramy. |
| Microsoft.Web/connections/*/read | Przeczytaj połączenia. |
| Microsoft.Web/customApis/*/read | Niestandardowy interfejs API odczytu. |
| Microsoft.Web/serverFarms/read | Pobiera właściwości planu usługi App Service |

## <a name="managed-identity-contributor"></a>Współautor tożsamości zarządzanych
Tworzenia, odczytu, aktualizacji i usuwania tożsamości przypisane przez użytkownika

| **Akcje** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="managed-identity-operator"></a>Zarządzane tożsamość — Operator
Przeczytaj i przypisz przypisać tożsamość użytkownika

| **Akcje** |  |
| --- | --- |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
| Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="monitoring-contributor"></a>Współautor monitorowania
Można odczytać wszystkich danych monitorowania i edytować ustawienia monitorowania. Zobacz też [Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń z monitorem Azure](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Akcje** |  |
| --- | --- |
| * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
| Microsoft.AlertsManagement/alerts/* |  |
| Microsoft.AlertsManagement/alertsSummary/* |  |
| Microsoft.Insights/AlertRules/* | Odczyt/zapis/usuwania reguł alertów. |
| Microsoft.Insights/components/* | Odczyt/zapis/usuwania składników usługi Application Insights. |
| Microsoft.Insights/DiagnosticSettings/* | Ustawienia diagnostyki odczytu/zapisu/usuwania. |
| Microsoft.Insights/eventtypes/* | Wyświetl zdarzenia dziennika aktywności (zdarzeń zarządzania) w ramach subskrypcji. To uprawnienie jest dotyczy zarówno programowe, jak i portalu dostęp do dziennika aktywności. |
| Microsoft.Insights/LogDefinitions/* | To uprawnienie jest wymagane dla użytkowników, którzy potrzebują dostępu do dzienników aktywności w portalu. Lista kategorii dziennika w dzienniku aktywności. |
| Microsoft.Insights/MetricDefinitions/* | Odczytaj definicje metryki (listy dostępnych typów metryki dla zasobu). |
| Microsoft.Insights/Metrics/* | Odczytać metryki dla zasobu. |
| Microsoft.Insights/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
| Microsoft.Insights/webtests/* | Testów sieci web usługi Application Insights dla odczytu/zapisu/usuwania. |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Pakiety rozwiązania analizy dzienników dla odczytu/zapisu/usuwania. |
| Microsoft.OperationalInsights/workspaces/savedSearches/* | Analiza dzienników odczytu/zapisu/usuwania zapisane wyszukiwania. |
| Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action | Pobiera klucze udostępnionego dla obszaru roboczego. Klucze te są używane do połączenia agentów Microsoft Operational Insights do obszaru roboczego. |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Konfiguracje wglądu magazynu analizy dzienników odczytu/zapisu/usuwania. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
| Microsoft.WorkloadMonitor/workloads/* |  |

## <a name="monitoring-reader"></a>Czytelnik monitorowania
Może czytać wszystkie dane monitorowania (metryki, dzienniki itp.). Zobacz też [Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń z monitorem Azure](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles).

| **Akcje** |  |
| --- | --- |
| * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
| Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="network-contributor"></a>Współautor sieci
Umożliwia zarządzanie sieciami, ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania roli |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.Network/* | Tworzenie i zarządzanie sieciami |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="new-relic-apm-account-contributor"></a>Współautor konta usługi New Relic APM
Umożliwia zarządzanie kontami i aplikacjami usługi New Relic Application Performance Management, ale nie umożliwia uzyskiwania dostępu do nich.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
| NewRelic.APM/accounts/* |  |

## <a name="redis-cache-contributor"></a>Współautor pamięci podręcznej Redis
Umożliwia zarządzanie pamięciami podręcznymi Redis, ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania roli |
| Microsoft.Cache/redis/* | Tworzenie i zarządzanie nimi pamięci podręczne Redis |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="scheduler-job-collections-contributor"></a>Współautor kolekcji zadań usługi Harmonogram
Umożliwia zarządzanie kolekcjami zadań usługi Scheduler, ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania roli |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Scheduler/jobcollections/* | Tworzenie i zarządzanie nimi kolekcji zadań |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="search-service-contributor"></a>Współautor usługi wyszukiwania
Umożliwia zarządzanie usługami Search, ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania roli |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Search/searchServices/* | Tworzenie i zarządzanie nimi usługi wyszukiwania |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="security-admin"></a>Administrator zabezpieczeń
W Centrum zabezpieczeń tylko: można wyświetlić zasady zabezpieczeń, wyświetlanie stanów zabezpieczeń, Edytuj zasady zabezpieczeń, wyświetlanie alertów i zalecenia, odrzucać alerty i zalecenia

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Authorization/policyAssignments/* | Tworzenie i zarządzanie nimi przypisania zasad |
| Microsoft.Authorization/policyDefinitions/* | Tworzenie i zarządzanie nimi definicje zasad |
| Microsoft.Authorization/policySetDefinitions/* | Tworzenie i zarządzanie nimi zestawów zasad |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.operationalInsights/workspaces/*/read | Wyświetl dane analizy dzienników |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Security/*/read | Składniki zabezpieczeń Odczytaj i zasady |
| Microsoft.Security/locations/alerts/dismiss/action | Odrzuć alert zabezpieczeń |
| Microsoft.Security/locations/tasks/dismiss/action | Odrzuć zalecana ze względów bezpieczeństwa |
| Microsoft.Security/policies/write | Aktualizacje zasad zabezpieczeń |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="security-manager"></a>Menedżer zabezpieczeń
Umożliwia zarządzanie składnikami zabezpieczeń, zasadami zabezpieczeń i maszynami wirtualnymi

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.ClassicCompute/*/read | Przeczytaj informacje o konfiguracji klasycznych maszyn wirtualnych |
| Microsoft.ClassicCompute/virtualMachines/*/write | Zapisać konfiguracji dla klasycznych maszyn wirtualnych |
| Microsoft.ClassicNetwork/*/read | Odczytać informacji o sieci klasycznego |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Security/* | Tworzenie i zarządzanie składników zabezpieczeń i zasady |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="security-reader"></a>Odczytywanie zabezpieczeń
W Centrum zabezpieczeń tylko: można wyświetlić zalecenia i alertów, widok zasady zabezpieczeń, wyświetlanie stanów zabezpieczeń, ale nie można wprowadzić zmian

| **Akcje** |  |
| --- | --- |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.operationalInsights/workspaces/*/read | Wyświetl dane analizy dzienników |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Security/*/read | Składniki zabezpieczeń Odczytaj i zasady |

## <a name="site-recovery-contributor"></a>Współautor usługi Site Recovery
Pozwala zarządzać usługą Site Recovery z wyjątkiem tworzenia magazynu i przypisywania ról

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest używane przez usługę operacji wewnętrznych |
| Microsoft.RecoveryServices/Vaults/certificates/write | Operacja aktualizacji zasobów certyfikatu aktualizuje certyfikat poświadczeń zasobów/magazynu. |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | Tworzenie i zarządzanie nimi rozszerzone informacje związane z magazynu |
| Microsoft.RecoveryServices/Vaults/read | Operacja Get magazynu pobiera obiekt reprezentujący zasobów platformy Azure typu "magazynu" |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Tworzenie i Zarządzanie tożsamościami w zarejestrowany |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Utwórz lub zaktualizuj ustawienia alertu replikacji |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Przeczytaj żadnych zdarzeń |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | Tworzenie i zarządzanie nimi sieci szkieletowej replikacji |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Tworzenie i zarządzanie nimi zadania replikacji |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | Tworzenie i zarządzanie zasadami replikacji |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Tworzenie i zarządzanie planami odzyskiwania |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Tworzenie i zarządzanie nimi magazynu konfiguracji z magazynu usług odzyskiwania |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Zwraca token informacje magazynie usług odzyskiwania. |
| Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Magazyn Token operacji można uzyskać tokenu magazynu dla operacji poziomu wewnętrznej bazy danych magazynu. |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Odczyt alertów dla magazynu usług odzyskiwania |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="site-recovery-operator"></a>Operator usługi Site Recovery
Pozwala przechodzić do trybu failover i przywracać sprawność po awarii, ale nie umożliwia wykonywania innych operacji zarządzania usługi Site Recovery

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
| Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest używane przez usługę operacji wewnętrznych |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
| Microsoft.RecoveryServices/Vaults/read | Operacja Get magazynu pobiera obiekt reprezentujący zasobów platformy Azure typu "magazynu" |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Wyniki operacji Get operacji można używać Pobierz stan operacji i wynik operacji asynchronicznie przesłane |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Kontenery pobrać operacji można używać Pobierz kontenery zarejestrowanych dla każdego zasobu. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Wszystkie ustawienia alertów do odczytu |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Przeczytaj żadnych zdarzeń |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Sprawdza spójność sieci szkieletowej |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Żadnych sieci szkieletowych do odczytu |
| Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Ponownie Skojarz bramy |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Odnów certyfikat sieci szkieletowej |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Przeczytaj żadnych sieci |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/Odczyt | Przeczytaj wszelkie mapowania sieci |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Wszystkie kontenery ochrony |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Wszystkie chronione elementy do odczytu |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Zastosuj punktu odzyskiwania |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Zatwierdzanie trybu failover |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planowany tryb Failover |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Wszystkie chronione elementy do odczytu |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Przeczytaj punktów odzyskiwania replikacji |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Naprawa replikacji |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | Włącz ponownie ochronę chronionego elementu |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testowanie trybu Failover |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Wyczyszczenia testu pracy awaryjnej |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Tryb failover |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizacja usługi mobilności |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Przeczytaj mapowań kontenera ochrony |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Przeczytaj wszystkich dostawców usług odzyskiwania |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Odśwież dostawcę |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Przeczytaj wszystkie klasyfikacje magazynu |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/Odczyt | Wszelkie mapowania klasyfikacji magazynu do odczytu |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Wszystkie zadania do odczytu |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | Tworzenie i zarządzanie nimi zadania replikacji |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Wszystkie zasady do odczytu |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Zatwierdzanie trybu failover planu odzyskiwania |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan odzyskiwania usługi planowanego trybu Failover |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Wszystkie plany odzyskiwania do odczytu |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Włącz ponownie ochronę planu odzyskiwania |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plan odzyskiwania trybu Failover testu |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan odzyskiwania testu czyszczenia trybu Failover |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan odzyskiwania trybu failover |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Odczyt alertów dla magazynu usług odzyskiwania |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Zwraca token informacje magazynie usług odzyskiwania. |
| Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Magazyn Token operacji można uzyskać tokenu magazynu dla operacji poziomu wewnętrznej bazy danych magazynu. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="site-recovery-reader"></a>Czytelnik usługi Site Recovery
Pozwala wyświetlać stan usługi Site Recovery, ale nie umożliwia wykonywania innych operacji zarządzania

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alertów dla magazynu usług odzyskiwania. |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read |  |
| Microsoft.RecoveryServices/Vaults/read | Operacja Get magazynu pobiera obiekt reprezentujący zasobów platformy Azure typu "magazynu" |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Wyniki operacji Get operacji można używać Pobierz stan operacji i wynik operacji asynchronicznie przesłane |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Kontenery pobrać operacji można używać Pobierz kontenery zarejestrowanych dla każdego zasobu. |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Wszystkie ustawienia alertów do odczytu |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | Przeczytaj żadnych zdarzeń |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | Żadnych sieci szkieletowych do odczytu |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Przeczytaj żadnych sieci |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/Odczyt | Przeczytaj wszelkie mapowania sieci |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | Wszystkie kontenery ochrony |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | Wszystkie chronione elementy do odczytu |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | Wszystkie chronione elementy do odczytu |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Przeczytaj punktów odzyskiwania replikacji |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | Przeczytaj mapowań kontenera ochrony |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Przeczytaj wszystkich dostawców usług odzyskiwania |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | Przeczytaj wszystkie klasyfikacje magazynu |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/Odczyt | Wszelkie mapowania klasyfikacji magazynu do odczytu |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Wszystkie zadania do odczytu |
| Microsoft.RecoveryServices/vaults/replicationJobs/read | Wszystkie zadania do odczytu |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | Wszystkie zasady do odczytu |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Wszystkie plany odzyskiwania do odczytu |
| Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Zwraca token informacje magazynie usług odzyskiwania. |
| Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
| Microsoft.RecoveryServices/Vaults/vaultTokens/read | Magazyn Token operacji można uzyskać tokenu magazynu dla operacji poziomu wewnętrznej bazy danych magazynu. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="sql-db-contributor"></a>Współautor bazy danych SQL
Umożliwia zarządzanie baz danych, ale nie do nich dostępu. Ponadto nie można zarządzać ich zasad związanych z zabezpieczeniami lub ich nadrzędnego serwerów SQL.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania roli |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/databases/* | Tworzenie i zarządzanie nimi baz danych SQL |
| Microsoft.Sql/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Nie można edytować zasady inspekcji |
| Microsoft.Sql/servers/databases/auditingSettings/* | Nie można edytować ustawień inspekcji |
| Microsoft.Sql/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji bazy danych obiektów blob |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Nie można edytować zasady połączeń |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Nie można edytować zasady maskowania danych |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Nie można edytować zasady alertów zabezpieczeń |
| Microsoft.Sql/servers/databases/securityMetrics/* | Nie można edytować metryki zabezpieczeń |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Menedżer zabezpieczeń SQL
Umożliwia zarządzanie zasadami serwerów i baz danych SQL związanymi z zabezpieczeniami, ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Microsoft odczytu autoryzacji |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Tworzy sprzężenie zasobów, takich jak konta magazynu lub bazy danych SQL do podsieci. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Sql/servers/auditingPolicies/* | Tworzenie i zarządzanie zasadami inspekcji serwera SQL |
| Microsoft.Sql/servers/auditingSettings/* | Tworzenie i zarządzanie nimi ustawienia inspekcji serwera SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Tworzenie i zarządzanie zasadami inspekcji bazy danych serwera SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Tworzenie i zarządzanie nimi ustawienia inspekcji bazy danych programu SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read | Odczytywanie rekordów inspekcji |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Tworzenie i zarządzanie zasadami połączenia bazy danych serwera SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Tworzenie i zarządzanie nimi danych bazy danych programu SQL server maskowania zasad |
| Microsoft.Sql/servers/databases/read | Zwraca listę baz danych lub pobiera właściwości dla określonej bazy danych. |
| Microsoft.Sql/servers/databases/schemas/read | Pobieranie listy schematów bazy danych |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read | Pobieranie listy kolumn tabeli. |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/read | Pobieranie listy tabel bazy danych |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Utwórz i Zarządzaj zasadami alertów zabezpieczeń bazy danych serwera SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Tworzenie i zarządzanie nimi metryki zabezpieczeń bazy danych serwera SQL |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/firewallRules/* |  |
| Microsoft.Sql/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
| Microsoft.Sql/servers/securityAlertPolicies/* | Utwórz i Zarządzaj zasadami alertów zabezpieczeń serwera SQL |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="sql-server-contributor"></a>Współautor serwera SQL Server
Umożliwia zarządzanie serwerami i bazami danych SQL, ale nie umożliwia uzyskiwania dostępu do nich ani do ich zasad związanych z zabezpieczeniami.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Sql/locations/*/read |  |
| Microsoft.Sql/servers/* | Tworzenie i zarządzanie serwerami programu SQL Server |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* | Nie można edytować zasady inspekcji serwera SQL |
| Microsoft.Sql/servers/auditingSettings/* | Nie można edytować ustawienia inspekcji serwera SQL |
| Microsoft.Sql/servers/databases/auditingPolicies/* | Nie można edytować zasady inspekcji bazy danych serwera SQL |
| Microsoft.Sql/servers/databases/auditingSettings/* | Nie można edytować ustawienia inspekcji bazy danych programu SQL server |
| Microsoft.Sql/servers/databases/auditRecords/read | Nie można odczytać wyników inspekcji |
| Microsoft.Sql/servers/databases/connectionPolicies/* | Nie można edytować zasad połączenia bazy danych serwera SQL |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Nie można edytować danych bazy danych programu SQL server maskowania zasad |
| Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* | Nie można edytować zasady alertów zabezpieczeń bazy danych serwera SQL |
| Microsoft.Sql/servers/databases/securityMetrics/* | Nie można edytować metryki zabezpieczeń bazy danych serwera SQL |
| Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
| Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
| Microsoft.Sql/servers/extendedAuditingSettings/* |  |
| Microsoft.Sql/servers/securityAlertPolicies/* | Nie można edytować zasady alertu zabezpieczeń serwera SQL |

## <a name="storage-account-contributor"></a>Współautor konta magazynu
Umożliwia zarządzanie kontami magazynu, ale nie dostęp do nich.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Wszystkie autoryzacji do odczytu |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Insights/diagnosticSettings/* | Zarządzanie ustawieniami diagnostycznych |
| Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Tworzy sprzężenie zasobów, takich jak konta magazynu lub bazy danych SQL do podsieci. |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Storage/storageAccounts/* | Tworzenie i zarządzanie nimi kont magazynu |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="storage-account-key-operator-service-role"></a>Rola usługi Operator kluczy kont magazynu
Operatorzy kluczy kont magazynu mogą wyświetlać listę kluczy dla kont magazynu i ponownie je generować

| **Akcje** |  |
| --- | --- |
| Microsoft.Storage/storageAccounts/listkeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
| Microsoft.Storage/storageAccounts/regeneratekey/action | Generuje ponownie klucze dostępu dla podanego konta magazynu. |

## <a name="support-request-contributor"></a>Współautor żądania pomocy technicznej
Umożliwia tworzenie żądań pomocy technicznej i zarządzanie nimi

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="traffic-manager-contributor"></a>Współautor usługi Traffic Manager
Umożliwia zarządzanie profilami usługi Traffic Manager, ale nie zapewnia kontroli dostępu do nich.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Role odczytu i przypisania ról |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Network/trafficManagerProfiles/* |  |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="user-access-administrator"></a>Administrator dostępu użytkowników
Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure.

| **Akcje** |  |
| --- | --- |
| * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
| Microsoft.Authorization/* | Zarządzanie autoryzacji |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="virtual-machine-administrator-login"></a>Identyfikator logowania administratora maszyny wirtualnej
-   Użytkownicy z tą rolą mieć możliwość logowania do maszyny wirtualnej z administratorem systemu Windows lub Linux głównego użytkownikowi uprawnienia.

| **Akcje** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/loginAsAdmin/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="virtual-machine-contributor"></a>Współautor maszyny wirtualnej
Umożliwia zarządzanie maszyn wirtualnych, ale nie do nich dostępu i nie sieci wirtualnej lub konta magazynu, które są połączone.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
| Microsoft.Compute/availabilitySets/* | Utwórz i Zarządzaj zestawami dostępności obliczeń |
| Microsoft.Compute/locations/* | Tworzenie i zarządzanie nimi lokalizacje obliczeń |
| Microsoft.Compute/virtualMachines/* | Tworzenie i zarządzanie maszynami wirtualnymi |
| Microsoft.Compute/virtualMachineScaleSets/* | Tworzenie i zarządzanie nimi zestawy skalowania maszyny wirtualnej |
| Microsoft.DevTestLab/schedules/* |  |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action | Dołącza do puli adresów zaplecza bramy aplikacji |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action | Dołącza do puli adresów zaplecza modułu równoważenia obciążenia |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action | Tworzy sprzężenie modułu równoważenia obciążenia ruchu przychodzącego pulę translacji nat |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action | Tworzy sprzężenie reguły nat dla ruchu przychodzącego modułu równoważenia obciążenia |
| Microsoft.Network/loadBalancers/probes/join/action | Zezwala przy użyciu sondy modułu równoważenia obciążenia. Na przykład z tą właściwością healthProbe uprawnienia skali maszyny Wirtualnej zestawu można odwoływać się sondy. |
| Microsoft.Network/loadBalancers/read | Pobiera definicję modułu równoważenia obciążenia |
| Microsoft.Network/locations/* | Tworzenie i zarządzanie nimi lokalizacji sieciowych |
| Microsoft.Network/networkInterfaces/* | Tworzenie i zarządzanie nimi interfejsy sieciowe |
| Microsoft.Network/networkSecurityGroups/join/action | Dołącza do sieciowej grupy zabezpieczeń |
| Microsoft.Network/networkSecurityGroups/read | Pobiera definicję grupy zabezpieczeń sieci |
| Microsoft.Network/publicIPAddresses/join/action | Dołącza do publicznego adresu ip |
| Microsoft.Network/publicIPAddresses/read | Pobiera definicję adres publiczny adres ip. |
| Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
| Microsoft.Network/virtualNetworks/subnets/join/action | Tworzy sprzężenie sieci wirtualnej |
| Microsoft.RecoveryServices/locations/* |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems / * / Odczyt |  |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/read | Zwraca szczegóły obiektu elemencie chroniony |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/ protectedItems/write | Tworzenie kopii zapasowej elementu chronione |
| Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Tworzenie kopii zapasowej opcje ochrony |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
| Microsoft.RecoveryServices/Vaults/backupPolicies/write | Tworzy zasady ochrony |
| Microsoft.RecoveryServices/Vaults/read | Operacja Get magazynu pobiera obiekt reprezentujący zasobów platformy Azure typu "magazynu" |
| Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
| Microsoft.RecoveryServices/Vaults/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn” |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Storage/storageAccounts/listKeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
| Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="virtual-machine-user-login"></a>Dane logowania użytkownika maszyny wirtualnej
Użytkownicy z tą rolą mieć możliwość logowania do maszyny wirtualnej przez użytkowników.

| **Akcje** |  |
| --- | --- |
| Microsoft.Compute/virtualMachines/login/action |  |
| Microsoft.Compute/virtualMachine/logon/action |  |

## <a name="web-plan-contributor"></a>Współautor planów sieci Web
Umożliwia zarządzanie planami sieci Web dla witryn sieci Web, ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
| Microsoft.Web/serverFarms/* | Tworzenie i zarządzanie nimi farmy serwerów |

## <a name="website-contributor"></a>Współautor witryny sieci Web
Umożliwia zarządzanie witrynami sieci Web (nie planami sieci Web), ale nie umożliwia uzyskiwania do nich dostępu.

| **Akcje** |  |
| --- | --- |
| Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
| Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi Insights reguły alertów |
| Microsoft.Insights/components/* | Tworzenie i zarządzanie nimi składniki Insights |
| Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
| Microsoft.Resources/deployments/* | Tworzenie i zarządzanie nimi wdrożenia grupy zasobów |
| Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
| Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
| Microsoft.Web/certificates/* | Tworzenie i zarządzanie certyfikatami witryny sieci Web |
| Microsoft.Web/listSitesAssignedToHostName/read | Pobierz nazwy lokacji przypisane do nazwy hosta. |
| Microsoft.Web/serverFarms/join/action |  |
| Microsoft.Web/serverFarms/read | Pobiera właściwości planu usługi App Service |
| Microsoft.Web/sites/* | Tworzenie i zarządzanie nimi witryn sieci Web (Tworzenie witryny wymaga także uprawnienia do zapisu do skojarzonego planu usługi App Service) |

## <a name="see-also"></a>Zobacz także
* [Kontrola dostępu oparta na rolach](role-assignments-portal.md): rozpoczynanie pracy z RBAC w portalu Azure.
* [Role niestandardowe w Azure RBAC](custom-roles.md): Dowiedz się, jak tworzyć role niestandardowe, aby spełniały Twoje potrzeby dostępu.
* [Tworzenie raportu historii zmian dostępu](change-history-report.md): informacje o zmieniania przypisań ról w RBAC.
* [Rozwiązywanie kontroli dostępu opartej na rolach](troubleshooting.md): Pobierz sugestie dotyczące rozwiązywania typowych problemów.
* [Uprawnienia w Centrum zabezpieczeń Azure](../security-center/security-center-permissions.md)
