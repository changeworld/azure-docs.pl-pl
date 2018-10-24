---
title: Wbudowane role zasobów platformy Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano wbudowane role kontroli dostępu opartej na rolach (RBAC) i zasobów platformy Azure. Wyświetla listę akcji, NotActions, elementy DataActions i NotDataActions.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/19/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: 72a8a09d04dc009598dafc35b65304662b7b8915
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/24/2018
ms.locfileid: "49955919"
---
# <a name="built-in-roles-for-azure-resources"></a>Wbudowane role zasobów platformy Azure
[Kontrola dostępu oparta na rolach (RBAC)](overview.md) ma kilka definicji wbudowanej roli, które można przypisać do użytkowników, grup i nazwy główne usług. Przypisania roli są sposób kontrolowania dostępu do zasobów na platformie Azure. Jeśli role wbudowane nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne [role niestandardowe](custom-roles.md).

Wbudowane role zawsze wciąż ewoluuje. Aby uzyskać najnowsze definicje ról, użyj [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) lub [Lista definicji roli az](/cli/azure/role/definition#az-role-definition-list).

## <a name="built-in-role-descriptions"></a>Opisy ról wbudowanych
Poniższa tabela zawiera krótkie opisy ról wbudowanych. Kliknij nazwę roli, aby wyświetlić listę `Actions`, `NotActions`, `DataActions`, i `NotDataActions` dla każdej roli.


| Wbudowana rola | Opis |
| --- | --- |
| [Właściciel](#owner) | Umożliwia zarządzanie wszystkim, w tym dostępem do zasobów. |
| [Współautor](#contributor) | Umożliwia zarządzanie wszystkim z wyjątkiem dostępu do zasobów. |
| [Czytelnik](#reader) | Umożliwia wyświetlanie wszystkiego, ale nie umożliwia wprowadzania jakichkolwiek zmian. |
| [AcrImageSigner](#acrimagesigner) | osoba podpisująca obraz ACR |
| [AcrQuarantineReader](#acrquarantinereader) | czytnik danych kwarantanny ACR |
| [AcrQuarantineWriter](#acrquarantinewriter) | moduł zapisywania danych kwarantanny ACR |
| [Współautor usługi API Management](#api-management-service-contributor) | Umożliwia zarządzanie usługami API Management, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Rola Operator usługi API Management](#api-management-service-operator-role) | Może zarządzać usługą, lecz nie interfejsami API |
| [Rola czytnik usługi API Management](#api-management-service-reader-role) | Dostęp tylko do odczytu do usługi i interfejsów API |
| [Współautor składników usługi Application Insights](#application-insights-component-contributor) | Umożliwia zarządzanie składnikami usługi Application Insights |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Nadaje użytkownikowi uprawnienie, aby wyświetlić i pobrać migawki debugowania zebranych za pomocą Application Insights Snapshot Debugger. Należy zauważyć, że te uprawnienia nie są uwzględnione w [właściciela](#owner) lub [Współautor](#contributor) ról. |
| [Operator zadań usługi Automation](#automation-job-operator) | Twórz zadania i zarządzaj nimi za pomocą elementów runbook usługi Automation. |
| [Operator usługi Automation](#automation-operator) | Operatorzy automatyzacji mogą uruchamiać, zatrzymywać, wstrzymywać i wznawiać zadania |
| [Operator elementów Runbook usługi Automation](#automation-runbook-operator) | Odczytuj właściwości elementu runbook, aby móc tworzyć zadania tego elementu. |
| [Rola administratora klastra Azure Kubernetes Service](#azure-kubernetes-service-cluster-admin-role) | Utwórz listę akcji poświadczenia administratora klastra. |
| [Rola użytkownika klastra Azure Kubernetes Service](#azure-kubernetes-service-cluster-user-role) | Utwórz listę akcji poświadczeń użytkownika klastra. |
| [Właściciel rejestracji w usłudze Azure Stack](#azure-stack-registration-owner) | Umożliwia zarządzanie rejestracjami w usłudze Azure Stack. |
| [Współautor kopii zapasowych](#backup-contributor) | Umożliwia zarządzanie usługami kopii zapasowych z wyjątkiem tworzenia magazynów i przyznawania dostępu innym osobom |
| [Operator kopii zapasowych](#backup-operator) | Umożliwia zarządzanie usługami kopii zapasowych z wyjątkiem usuwania kopii zapasowych, tworzenia magazynów i przyznawania dostępu innym osobom |
| [Czytelnik kopii zapasowych](#backup-reader) | Może wyświetlać usługi kopii zapasowych, ale nie może wprowadzać zmian |
| [Czytelnik rozliczenia](#billing-reader) | Umożliwia odczytywanie danych dotyczących rozliczeń |
| [BizTalk Contributor](#biztalk-contributor) | Umożliwia zarządzanie usługami BizTalk Services, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor punktu końcowego usługi CDN](#cdn-endpoint-contributor) | Może zarządzać punktami końcowymi usługi CDN, lecz nie może przyznawać dostępu innym użytkownikom. |
| [Czytelnik punktu końcowego usługi CDN](#cdn-endpoint-reader) | Może wyświetlać punkty końcowe usługi CDN, ale nie może wprowadzać zmian. |
| [Współautor profilu CDN](#cdn-profile-contributor) | Może zarządzać profilami usługi CDN i ich punktami końcowymi, ale nie może przyznawać dostępu innym użytkownikom. |
| [Czytelnik profilu usługi CDN](#cdn-profile-reader) | Może wyświetlać profile usługi CDN i ich punkty końcowe, ale nie może wprowadzać zmian. |
| [Współautor klasycznej sieci](#classic-network-contributor) | Umożliwia zarządzanie klasycznymi sieciami, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor klasycznego konta magazynu](#classic-storage-account-contributor) | Umożliwia zarządzanie kontami klasycznego magazynu, ale nie dostęp do nich. |
| [Rola usługi Operator kluczy kont magazynu Classic](#classic-storage-account-key-operator-service-role) | Operatorzy kluczy klasycznych kont magazynu mogą wyświetlać listę kluczy dla klasycznych kont magazynu i ponownie je generować |
| [Współautor klasycznej maszyny wirtualnej](#classic-virtual-machine-contributor) | Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie dostęp do nich ani do sieci wirtualnych i konta magazynu, z którymi są połączone. |
| [Współautor usługi cognitive Services](#cognitive-services-contributor) | Umożliwia tworzenie, odczytu, aktualizowanie, usuwanie i zarządzanie kluczami usługi Cognitive Services. |
| [Użytkownik usług cognitive Services](#cognitive-services-user) | Umożliwia odczyt i wyświetlać listę kluczy usług Cognitive Services. |
| [Rola czytelnika konta usługi cosmos DB](#cosmos-db-account-reader-role) | Może odczytywać dane z konta usługi Azure Cosmos DB. Zobacz [Współautor konta usługi DocumentDB](#documentdb-account-contributor) do zarządzania kontami usługi Azure Cosmos DB. |
| [Usługa Cost Management współautora](#cost-management-contributor) | Można wyświetlić koszty konfiguracji i zarządzania nią koszt (np. budżet, eksportuje) |
| [Usługa Cost Management czytnika](#cost-management-reader) | Można wyświetlić dane kosztów i konfiguracji (np. budżet, eksportuje) |
| [Współautora usługi Data Box](#data-box-contributor) | Umożliwia zarządzanie wszystkim, w obszarze usługa Data Box, z wyjątkiem przyznawania dostępu innym osobom. |
| [Czytnik pole danych](#data-box-reader) | Pozwala zarządzać usługa Data Box, z wyjątkiem tworzenia kolejności lub edytowania szczegółów zamówienia i przyznawania dostępu innym osobom. |
| [Współautor Data Factory](#data-factory-contributor) | Umożliwia zarządzanie fabrykami danych, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Data Lake Analytics dewelopera](#data-lake-analytics-developer) | Umożliwia przesyłanie własnych zadań, monitorowanie ich i zarządzanie nimi, ale nie tworzenie ani usuwanie kont usługi Data Lake Analytics. |
| [Purger danych](#data-purger) | Może czyścić dane analizy |
| [Użytkownik usługi DevTest Labs](#devtest-labs-user) | Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w usłudze Azure DevTest Labs. |
| [Współautor strefy DNS](#dns-zone-contributor) | Umożliwia zarządzanie strefami DNS i zestawami rekordów w usłudze Azure DNS, ale nie zapewnia kontroli dostępu do nich. |
| [Współautor konta usługi DocumentDB](#documentdb-account-contributor) | Można zarządzać kontami usługi Azure Cosmos DB. Usługa Azure Cosmos DB jest znana wcześniej jako baza danych DocumentDB. |
| [Współautor usługi HDInsight domeny](#hdinsight-domain-services-contributor) | Może odczytywać powiązanych operacji tworzenia, modyfikowania i usuwania usług domenowych służące do pakiet HDInsight Enterprise Security |
| [Współautor konta usługi systemy inteligentne](#intelligent-systems-account-contributor) | Umożliwia zarządzanie kontami usługi Intelligent Systems, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor magazynu kluczy](#key-vault-contributor) | Umożliwia zarządzanie magazynami kluczy, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Twórca laboratorium](#lab-creator) | Służy do tworzenia i usuwania zarządzanych laboratoriów oraz zarządzania nimi w ramach konta usługi Azure Lab. |
| [Współautor usługi log Analytics](#log-analytics-contributor) | Współautor usługi log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia do maszyn wirtualnych; Odczytywanie kluczy kont magazynu, aby można było skonfigurować zbieranie dzienników z usługi Azure Storage; Tworzenie i konfigurowanie kont usługi Automation; Dodawanie rozwiązań. i konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
| [Czytelnik usługi log Analytics](#log-analytics-reader) | Czytelnik usługi Log Analytics może wyświetlać i wyszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym konfigurację diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
| [Współautor aplikacji logiki](#logic-app-contributor) | Umożliwia zarządzanie aplikacjami logiki, lecz nie dostęp do nich. |
| [Operator aplikacji logiki](#logic-app-operator) | Umożliwia odczytywanie, włączanie i wyłączanie aplikacji logiki. |
| [Rola Operator aplikacji zarządzanej](#managed-application-operator-role) | Umożliwia odczytywanie i wykonywać akcje na zasobach zarządzanej aplikacji |
| [Czytnik zarządzanych aplikacji](#managed-applications-reader) | Umożliwia odczyt zasobów zarządzanych aplikacji i żądanie dostępu JIT. |
| [Współautor tożsamości zarządzanych](#managed-identity-contributor) | Tworzenie, odczytywanie, aktualizowanie i usuwanie tożsamości przypisanych przez użytkownika |
| [Operator tożsamości zarządzanych](#managed-identity-operator) | Odczytaj i przypisz tożsamość przypisaną przez użytkownika |
| [Współautor grupy zarządzania](#management-group-contributor) | Rola współautora grupy zarządzania |
| [Czytnik grupy zarządzania](#management-group-reader) | Rola czytelnika grupy zarządzania |
| [Współautor monitorowania](#monitoring-contributor) | Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też [Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń za pomocą usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Monitorowanie metryk wydawcy](#monitoring-metrics-publisher) | Umożliwia publikowanie metryk dotyczących zasobów platformy Azure |
| [Czytelnik monitorowania](#monitoring-reader) | Może odczytywać wszystkie dane monitorowania (metryk, dzienników itp.). Zobacz też [Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń za pomocą usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
| [Współautor sieci](#network-contributor) | Umożliwia zarządzanie sieciami, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Nowe Współautor konta usługi Relic APM](#new-relic-apm-account-contributor) | Umożliwia zarządzanie kontami i aplikacjami usługi New Relic Application Performance Management, ale nie umożliwia uzyskiwania dostępu do nich. |
| [Czytnik i dostęp do danych](#reader-and-data-access) | Pozwala przeglądać wszystko, ale nie będzie można usunąć lub utworzyć konto magazynu lub zawartego zasobu. Będzie również umożliwiać dostępu odczytu i zapisu do wszystkich danych znajdujących się na koncie magazynu za pośrednictwem dostępu do kluczy konta magazynu. |
| [Współautor pamięci podręcznej redis](#redis-cache-contributor) | Umożliwia zarządzanie pamięciami podręcznymi Redis, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor zasad zasobów (wersja zapoznawcza)](#resource-policy-contributor-preview) | (Wersja zapoznawcza) Użytkownicy uzupełnieni z umowy EA z prawami do tworzenia/modyfikowania zasad zasobów, tworzenia biletów pomocy technicznej i odczytywania zasobów/hierarchii. |
| [Współautor kolekcji zadań usługi Scheduler](#scheduler-job-collections-contributor) | Umożliwia zarządzanie kolekcjami zadań usługi Scheduler, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor usługi wyszukiwania](#search-service-contributor) | Umożliwia zarządzanie usługami Search, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Administrator zabezpieczeń](#security-admin) | W usłudze Security Center tylko: można wyświetlić zasady zabezpieczeń, widoku stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlanie alertów i zaleceń, Odrzuć alerty i zalecenia |
| [Menedżer zabezpieczeń](#security-manager) | Umożliwia zarządzanie składnikami zabezpieczeń, zasadami zabezpieczeń i maszynami wirtualnymi |
| [Czytelnik zabezpieczeń](#security-reader) | W usłudze Security Center tylko: mogą wyświetlać, zaleceń i alertów, widok zasady zabezpieczeń, wyświetlanie stanów zabezpieczeń, ale nie może wprowadzać zmian |
| [Współautor usługi Site Recovery](#site-recovery-contributor) | Pozwala zarządzać usługą Site Recovery z wyjątkiem tworzenia magazynu i przypisywania ról |
| [Operator usługi Site Recovery](#site-recovery-operator) | Pozwala przechodzić do trybu failover i przywracać sprawność po awarii, ale nie umożliwia wykonywania innych operacji zarządzania usługi Site Recovery |
| [Czytelnik usługi Site Recovery](#site-recovery-reader) | Pozwala wyświetlać stan usługi Site Recovery, ale nie umożliwia wykonywania innych operacji zarządzania |
| [Współautor bazy danych SQL](#sql-db-contributor) | Umożliwia zarządzanie bazami danych SQL, ale nie umożliwia uzyskiwania dostępu do nich. Ponadto nie można zarządzać ich zasad związanych z bezpieczeństwem ani nadrzędnymi serwerami SQL. |
| [SQL Security Manager](#sql-security-manager) | Umożliwia zarządzanie zasadami serwerów i baz danych SQL związanymi z zabezpieczeniami, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor serwera SQL Server](#sql-server-contributor) | Umożliwia zarządzanie serwerami i bazami danych SQL, ale nie umożliwia uzyskiwania dostępu do nich ani do ich zasad związanych z zabezpieczeniami. |
| [Współautor konta magazynu](#storage-account-contributor) | Umożliwia zarządzanie kontami magazynu, ale nie dostęp do nich. |
| [Rola usługi Operator kluczy kont magazynu](#storage-account-key-operator-service-role) | Operatorzy kluczy kont magazynu mogą wyświetlać listę kluczy dla kont magazynu i ponownie je generować |
| [Współautor danych obiektu Blob Storage (wersja zapoznawcza)](#storage-blob-data-contributor-preview) | Zezwala na dostęp do odczytu, zapisu i usuwania do kontenerów obiektów blob i danych usługi Azure Storage |
| [Czytnik danych obiektu Blob Storage (wersja zapoznawcza)](#storage-blob-data-reader-preview) | Zezwala na dostęp do odczytu do kontenerów obiektów blob i danych usługi Azure Storage |
| [Współautor danych kolejki magazynu (wersja zapoznawcza)](#storage-queue-data-contributor-preview) | Zezwala na dostęp do odczytu, zapisu i usuwania do kolejek i komunikatów kolejek usługi Azure Storage |
| [Czytnik danych kolejki magazynu (wersja zapoznawcza)](#storage-queue-data-reader-preview) | Zezwala na dostęp do odczytu do kolejek i komunikatów kolejek usługi Azure Storage |
| [Współautor żądania pomocy technicznej](#support-request-contributor) | Umożliwia tworzenie żądań pomocy technicznej i zarządzanie nimi |
| [Współautor usługi Traffic Manager](#traffic-manager-contributor) | Umożliwia zarządzanie profilami usługi Traffic Manager, ale nie zapewnia kontroli dostępu do nich. |
| [Administrator dostępu użytkowników](#user-access-administrator) | Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure. |
| [Logowanie administratora maszyny wirtualnej](#virtual-machine-administrator-login) | Wyświetl maszyny wirtualne w portalu i zaloguj się jako administrator |
| [Współautor maszyny wirtualnej](#virtual-machine-contributor) | Umożliwia zarządzanie maszynami wirtualnymi, ale nie dostęp do nich ani do sieci wirtualnych i konta magazynu, z którymi są połączone. |
| [Logowanie użytkownika maszyny wirtualnej](#virtual-machine-user-login) | Wyświetl maszyny wirtualne w portalu i zaloguj się jako zwykli użytkownicy. |
| [Współautor planów sieci Web](#web-plan-contributor) | Umożliwia zarządzanie planami sieci Web dla witryn sieci Web, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor witryny sieci Web](#website-contributor) | Umożliwia zarządzanie witrynami sieci Web (nie planami sieci Web), ale nie umożliwia uzyskiwania do nich dostępu. |


## <a name="owner"></a>Właściciel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie wszystkim, w tym dostępem do zasobów. |
> | **Identyfikator** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Akcje** |  |
> | * | Tworzenie i zarządzanie zasobami wszystkich typów |

## <a name="contributor"></a>Współautor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie wszystkim z wyjątkiem dostępu do zasobów. |
> | **Identyfikator** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Akcje** |  |
> | * | Tworzenie i zarządzanie zasobami wszystkich typów |
> | **notActions** |  |
> | Microsoft.Authorization/*/Delete | Nie można usunąć ról i przypisań ról |
> | Microsoft.Authorization/*/Write | Nie można utworzyć ról i przypisań ról |
> | Microsoft.Authorization/elevateAccess/Action | Przyznaje osobie wywołującej uprawnienia administratora dostępu użytkowników w zakresie dzierżawy |
> | Microsoft.Blueprint/blueprintAssignments/write | Utwórz lub zaktualizuj wszelkie artefakty strategii |
> | Microsoft.Blueprint/blueprintAssignments/delete | Usuń wszelkie artefakty strategii |

## <a name="reader"></a>Czytelnik
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia wyświetlanie wszystkiego, ale nie umożliwia wprowadzania jakichkolwiek zmian. |
> | **Identyfikator** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Akcje** |  |
> | * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych. |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | osoba podpisująca obraz ACR |
> | **Identyfikator** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | czytnik danych kwarantanny ACR |
> | **Identyfikator** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | moduł zapisywania danych kwarantanny ACR |
> | **Identyfikator** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/*/write |  |
> | Microsoft.ContainerRegistry/registries/*/read |  |

## <a name="api-management-service-contributor"></a>Współautor usługi API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie usługami API Management, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Akcje** |  |
> | Microsoft.ApiManagement/service/* | Tworzenie i zarządzanie nimi usługi API Management |
> | Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="api-management-service-operator-role"></a>Rola Operator usługi API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może zarządzać usługą, lecz nie interfejsami API |
> | **Identyfikator** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Akcje** |  |
> | Microsoft.ApiManagement/service/*/read | Wystąpienia usługi odczytu API Management |
> | Microsoft.ApiManagement/service/backup/action | Kopii zapasowych usługi API Management do określonego kontenera w użytkownika podane konto magazynu |
> | Microsoft.ApiManagement/service/delete | Usuń wystąpienie usługi API Management |
> | Microsoft.ApiManagement/service/managedeployments/action | Zmiana jednostki SKU/jednostek, dodawać i usuwać regionalnych wdrożeń usługi API Management |
> | Microsoft.ApiManagement/service/read | Odczytywanie metadanych dla wystąpienia usługi API Management |
> | Microsoft.ApiManagement/service/restore/action | Przywracanie usługi API Management z określonego kontenera użytkownika podane konto magazynu |
> | Microsoft.ApiManagement/service/updatecertificate/action | Przekaż certyfikat SSL dla usługi API Management |
> | Microsoft.ApiManagement/service/updatehostname/action | Konfigurowanie, zaktualizować lub usunąć niestandardowe nazwy domen dla usługi API Management |
> | Microsoft.ApiManagement/service/write | Utwórz nowe wystąpienie usługi API Management |
> | Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | **notActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Pobierz listę kluczy użytkowników |

## <a name="api-management-service-reader-role"></a>Rola Czytnik usługi API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Dostęp tylko do odczytu do usługi i interfejsów API |
> | **Identyfikator** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Akcje** |  |
> | Microsoft.ApiManagement/service/*/read | Wystąpienia usługi odczytu API Management |
> | Microsoft.ApiManagement/service/read | Odczytywanie metadanych dla wystąpienia usługi API Management |
> | Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | **notActions** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Pobierz listę kluczy użytkowników |

## <a name="application-insights-component-contributor"></a>Współautor składników usługi Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie składnikami usługi Application Insights |
> | **Identyfikator** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.Insights/components/* | Tworzenie i zarządzanie składnikami usługi Insights |
> | Microsoft.Insights/webtests/* | Tworzenie i zarządzanie nimi testy sieci web |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Nadaje użytkownikowi uprawnienie, aby wyświetlić i pobrać migawki debugowania zebranych za pomocą Application Insights Snapshot Debugger. Należy zauważyć, że te uprawnienia nie są uwzględnione w [właściciela](#owner) lub [Współautor](#contributor) ról. |
> | **Identyfikator** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="automation-job-operator"></a>Operator zadań usługi Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Twórz zadania i zarządzaj nimi za pomocą elementów runbook usługi Automation. |
> | **Identyfikator** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Microsoft.Automation/automationAccounts/jobs/read | Pobiera zadanie usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Wznawia zadanie usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Zatrzymuje zadanie usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadań usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Wstrzymuje zadanie usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Tworzy zadanie usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Pobiera dane wyjściowe zadania |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="automation-operator"></a>Operator usługi
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Operatorzy automatyzacji mogą uruchamiać, zatrzymywać, wstrzymywać i wznawiać zadania |
> | **Identyfikator** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Microsoft.Automation/automationAccounts/jobs/read | Pobiera zadanie usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Wznawia zadanie usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Zatrzymuje zadanie usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadań usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Wstrzymuje zadanie usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Tworzy zadanie usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Pobiera harmonogram zadań usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Tworzy harmonogram zadań usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Pobiera obszar roboczy połączony z kontem usługi automation |
> | Microsoft.Automation/automationAccounts/read | Pobiera konto usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/runbooks/read | Pobiera element runbook usługi Automation |
> | Microsoft.Automation/automationAccounts/schedules/read | Pobiera zasób harmonogramu usługi Azure Automation |
> | Microsoft.Automation/automationAccounts/schedules/write | Tworzy lub aktualizuje zasób harmonogramu usługi Azure Automation |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Automation/automationAccounts/jobs/output/read | Pobiera dane wyjściowe zadania |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="automation-runbook-operator"></a>Operator elementów runbook usługi Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Odczytuj właściwości elementu runbook, aby móc tworzyć zadania tego elementu. |
> | **Identyfikator** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Automation/automationAccounts/runbooks/read | Pobiera element runbook usługi Automation |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Rola administratora klastra Azure Kubernetes Service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Utwórz listę akcji poświadczenia administratora klastra. |
> | **Identyfikator** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Akcje** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Lista poświadczeń clusterAdmin zarządzanego klastra |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Rola użytkownika klastra Azure Kubernetes Service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Utwórz listę akcji poświadczeń użytkownika klastra. |
> | **Identyfikator** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Akcje** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Lista poświadczeń clusterUser zarządzanego klastra |

## <a name="azure-stack-registration-owner"></a>Właściciel rejestracji w usłudze Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie rejestracjami w usłudze Azure Stack. |
> | **Identyfikator** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Akcje** |  |
> | Microsoft.AzureStack/registrations/products/listDetails/action | Pobiera rozszerzone szczegóły produktu usługi Azure Stack w portalu Marketplace |
> | Microsoft.AzureStack/registrations/products/read | Pobiera właściwości produktu usługi Azure Stack w portalu Marketplace |
> | Microsoft.AzureStack/registrations/read | Pobiera właściwości rejestracji w usłudze Azure Stack |

## <a name="backup-contributor"></a>Współautor kopii zapasowych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie usługami kopii zapasowych z wyjątkiem tworzenia magazynów i przyznawania dostępu innym osobom |
> | **Identyfikator** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Zarządzanie wynikami operacja zarządzania kopiami zapasowymi |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Tworzenie kontenerów i zarządzanie nimi kopii zapasowej w kopii zapasowej sieci szkieletowej magazynu usług Recovery Services |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Tworzenie i zarządzanie nimi zadania tworzenia kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportuj zadania |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Zwraca wynik operacji eksportowania zadania. |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Tworzenie i zarządzanie nimi metadane związane z zarządzaniem kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Tworzenie i zarządzanie nimi wyniki operacji zarządzania kopiami zapasowymi |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Tworzenie i zarządzanie zasadami tworzenia kopii zapasowych |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Tworzenie i zarządzanie nimi elementy, które można utworzyć kopię zapasową |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Tworzenie i zarządzanie nimi elementy kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Tworzenie kontenerów i zarządzanie nimi zawierający elementy kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dotyczące chronionych elementów i serwerów chronionych usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Tworzenie i zarządzanie certyfikatami związane z kopiami zapasowymi w magazynie usługi Recovery Services |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Tworzenie i zarządzanie nimi rozszerzone informacje związane z magazynu |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "Magazyn" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Tworzenie i Zarządzanie tożsamościami zarejestrowane |
> | Microsoft.RecoveryServices/Vaults/usages/* | Tworzenie i zarządzanie nimi użycia magazynu usług Recovery Services |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Weryfikowanie operacji chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca operacji tworzenia kopii zapasowej odzyskiwania stanu usługi magazynu. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Pobierz wszystkie kontenery z możliwością objęcia ochroną |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Sprawdź stan kopii zapasowej dla magazynów usługi Recovery Services |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Weryfikuj funkcje |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Microsoft.RecoveryServices/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listę wszystkich kopii zapasowych opcjami ochrony |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="backup-operator"></a>Operator kopii zapasowych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie usługami kopii zapasowych z wyjątkiem usuwania kopii zapasowych, tworzenia magazynów i przyznawania dostępu innym osobom |
> | **Identyfikator** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Wykonuje kopię zapasową elementu chronionego. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan operacji wykonanej na elementach chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Ustanów natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Przywróć punkty odzyskiwania dla elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Wycofaj natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Utwórz chroniony element kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkie zarejestrowane kontenery |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Tworzenie i zarządzanie nimi zadania tworzenia kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportuj zadania |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Zwraca wynik operacji eksportowania zadania. |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Zwraca metadane zarządzania kopiami zapasowymi magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Tworzenie i zarządzanie nimi wyniki operacji zarządzania kopiami zapasowymi |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Tworzenie i zarządzanie nimi elementy, które można utworzyć kopię zapasową |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dotyczące chronionych elementów i serwerów chronionych usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "Magazyn" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyników operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Pobierz kontenery operacji umożliwia pobranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operacja Rejestruj kontener usługi umożliwia rejestrowanie kontenera za pomocą usługi odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Weryfikowanie operacji chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca operacji tworzenia kopii zapasowej odzyskiwania stanu usługi magazynu. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Pobierz stan operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Tworzy zarejestrowany kontener |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Wykonaj zapytanie dotyczące obciążeń w kontenerze |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Utwórz opcję ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Pobierz opcję ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Pobierz wszystkie kontenery z możliwością objęcia ochroną |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Sprawdź stan kopii zapasowej dla magazynów usługi Recovery Services |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Weryfikuj funkcje |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Microsoft.RecoveryServices/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listę wszystkich kopii zapasowych opcjami ochrony |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="backup-reader"></a>Czytelnik kopii zapasowych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może wyświetlać usługi kopii zapasowych, ale nie może wprowadzać zmian |
> | **Identyfikator** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan operacji wykonanej na elementach chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkie zarejestrowane kontenery |
> | Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read | Zwraca wynik operacji zadania. |
> | Microsoft.RecoveryServices/Vaults/backupJobs/read | Zwraca wszystkie obiekty zadań |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportuj zadania |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/operationResults/read | Zwraca wynik operacji eksportowania zadania. |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | Zwraca metadane zarządzania kopiami zapasowymi magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Zwraca wynik operacji tworzenia kopii zapasowej magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania dotyczące chronionych elementów i serwerów chronionych usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery services. |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "Magazyn" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyników operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Pobierz kontenery operacji umożliwia pobranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Zwraca konfigurację magazynu dla odzyskiwania usług magazynu. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Zwraca konfigurację dla odzyskiwania usług magazynu. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca operacji tworzenia kopii zapasowej odzyskiwania stanu usługi magazynu. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Pobierz stan operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Pobierz opcję ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Sprawdź stan kopii zapasowej dla magazynów usługi Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Microsoft.RecoveryServices/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Listę wszystkich kopii zapasowych opcjami ochrony |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |

## <a name="billing-reader"></a>Czytelnik rozliczeń
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczytywanie danych dotyczących rozliczeń |
> | **Identyfikator** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Billing/*/read | Przeczytaj informacja o rozliczeniach |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Wyświetlanie listy grup zarządzania dla tego uwierzytelnionego użytkownika. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="biztalk-contributor"></a>Współautor usługi BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie usługami BizTalk Services, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.BizTalkServices/BizTalk/* | Tworzenie i zarządzanie usługą BizTalk services |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cdn-endpoint-contributor"></a>Współautor punktu końcowego usługi CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może zarządzać punktami końcowymi usługi CDN, lecz nie może przyznawać dostępu innym użytkownikom. |
> | **Identyfikator** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cdn-endpoint-reader"></a>Czytelnik punktu końcowego usługi CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może wyświetlać punkty końcowe usługi CDN, ale nie może wprowadzać zmian. |
> | **Identyfikator** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cdn-profile-contributor"></a>Współautor profilu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może zarządzać profilami usługi CDN i ich punktami końcowymi, ale nie może przyznawać dostępu innym użytkownikom. |
> | **Identyfikator** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cdn-profile-reader"></a>Czytelnik profilu usługi CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może wyświetlać profile usługi CDN i ich punkty końcowe, ale nie może wprowadzać zmian. |
> | **Identyfikator** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="classic-network-contributor"></a>Współautor klasycznej sieci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie klasycznymi sieciami, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
> | Microsoft.ClassicNetwork/* | Tworzenie i zarządzanie nimi w klasycznych sieciach |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="classic-storage-account-contributor"></a>Współautor klasycznego konta magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie kontami klasycznego magazynu, ale nie dostęp do nich. |
> | **Identyfikator** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
> | Microsoft.ClassicStorage/storageAccounts/* | Tworzenie i zarządzanie kontami magazynu |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="classic-storage-account-key-operator-service-role"></a>Rola usługi Operator kluczy klasycznych kont magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Operatorzy kluczy klasycznych kont magazynu mogą wyświetlać listę kluczy dla klasycznych kont magazynu i ponownie je generować |
> | **Identyfikator** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Akcje** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Wyświetla klucze dostępu dla kont magazynu. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Generuje ponownie istniejące klucze dostępu dla konta magazynu. |

## <a name="classic-virtual-machine-contributor"></a>Współautor klasycznej maszyny wirtualnej
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie dostęp do nich ani do sieci wirtualnych i konta magazynu, z którymi są połączone. |
> | **Identyfikator** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
> | Microsoft.ClassicCompute/domainNames/* | Tworzenie i zarządzanie nazwami domen classic compute |
> | Microsoft.ClassicCompute/virtualMachines/* | Tworzenie i zarządzanie maszynami wirtualnymi |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Połącz zarezerwowany adres IP |
> | Microsoft.ClassicNetwork/reservedIps/read | Pobiera zastrzeżone adresy IP |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Umożliwia dołączenie do sieci wirtualnej. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Pobiera sieć wirtualną. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Zwraca dysk konta magazynu. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Zwraca obraz konta magazynu. (Przestarzałe. Użyj "Microsoft.ClassicStorage/storageAccounts/vmImages") |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla klucze dostępu dla kont magazynu. |
> | Microsoft.ClassicStorage/storageAccounts/read | Zwraca konto magazynu z podanym kontem. |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cognitive-services-contributor"></a>Współautor usługi cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia tworzenie, odczytu, aktualizowanie, usuwanie i zarządzanie kluczami usługi Cognitive Services. |
> | **Identyfikator** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Pobiera funkcje subskrypcji. |
> | Microsoft.Features/providers/features/read | Pobiera funkcję subskrypcji danego dostawcy zasobów. |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne programu Analysis Server |
> | Microsoft.Insights/logDefinitions/read | Przeczytaj definicje dzienników |
> | Microsoft.Insights/metricdefinitions/read | Przeczytaj definicje metryk |
> | Microsoft.Insights/metrics/read | Odczytaj metryki |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/deployments/operations/read | Pobiera operacje wdrażania lub wyświetla ich listę. |
> | Microsoft.Resources/subscriptions/operationresults/read | Pobiera wyniki operacji subskrypcji. |
> | Microsoft.Resources/subscriptions/read | Pobiera listę subskrypcji. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cognitive-services-user"></a>Użytkownik usług cognitive Services
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczyt i wyświetlać listę kluczy usług Cognitive Services. |
> | **Identyfikator** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Akcje** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Wylicz klucze |
> | Microsoft.Insights/metricdefinitions/read | Przeczytaj definicje metryk |
> | Microsoft.Insights/metrics/read | Odczytaj metryki |
> | Microsoft.Insights/alertRules/read | Odczytaj alert dotyczący metryki klasycznej |
> | Microsoft.Insights/diagnosticSettings/read | Odczytaj ustawienie diagnostyczne zasobu |
> | Microsoft.Insights/logDefinitions/read | Przeczytaj definicje dzienników |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/operations/read | Pobiera operacje wdrażania lub wyświetla ich listę. |
> | Microsoft.Resources/subscriptions/operationresults/read | Pobiera wyniki operacji subskrypcji. |
> | Microsoft.Resources/subscriptions/read | Pobiera listę subskrypcji. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cosmos-db-account-reader-role"></a>Rola czytelnika konta usługi Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może odczytywać dane z konta usługi Azure Cosmos DB. Zobacz [Współautor konta usługi DocumentDB](#documentdb-account-contributor) do zarządzania kontami usługi Azure Cosmos DB. |
> | **Identyfikator** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról przeczytasz uprawnienia udzielone do poszczególnych użytkowników |
> | Microsoft.DocumentDB/*/read | Przeczytaj dowolnej kolekcji |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Odczytuje bazę danych, klucze tylko do odczytu konta. |
> | Microsoft.Insights/MetricDefinitions/read | Przeczytaj definicje metryk |
> | Microsoft.Insights/Metrics/read | Odczytaj metryki |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cost-management-contributor"></a>Usługa Cost Management współautora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Można wyświetlić koszty konfiguracji i zarządzania nią koszt (np. budżet, eksportuje) |
> | **Identyfikator** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Akcje** |  |
> | Microsoft.Consumption/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft.Billing/billingPeriods/read | Wyświetla listę dostępnych okresów rozliczeniowych |
> | Microsoft.Resources/subscriptions/read | Pobiera listę subskrypcji. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="cost-management-reader"></a>Usługa Cost Management czytnika
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Można wyświetlić dane kosztów i konfiguracji (np. budżet, eksportuje) |
> | **Identyfikator** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Akcje** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Billing/billingPeriods/read | Wyświetla listę dostępnych okresów rozliczeniowych |
> | Microsoft.Resources/subscriptions/read | Pobiera listę subskrypcji. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="data-box-contributor"></a>Współautora usługi Data Box
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie wszystkim, w obszarze usługa Data Box, z wyjątkiem przyznawania dostępu innym osobom. |
> | **Identyfikator** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | Microsoft.Databox/* |  |

## <a name="data-box-reader"></a>Czytnik pole danych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Pozwala zarządzać usługa Data Box, z wyjątkiem tworzenia kolejności lub edytowania szczegółów zamówienia i przyznawania dostępu innym osobom. |
> | **Identyfikator** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Databox/*/read |  |
> | Microsoft.Databox/jobs/listsecrets/action |  |
> | Microsoft.Databox/jobs/listcredentials/action | Wyświetla listę poświadczeń nieszyfrowanych, związanych z zamówieniem. |
> | Microsoft.Databox/locations/availableSkus/action | Ta metoda zwraca listę dostępnych jednostek SKU. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="data-factory-contributor"></a>Współautor Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie fabrykami danych, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 673868aa-7521-48A0-acc6-0f60742d39f5 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.DataFactory/dataFactories/* | Tworzenie i zarządzanie fabryki danych i zasobów podrzędnych w nich. |
> | Microsoft.DataFactory/factories/* | Tworzenie i zarządzanie fabryki danych i zasobów podrzędnych w nich. |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="data-lake-analytics-developer"></a>Deweloper usługi Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia przesyłanie własnych zadań, monitorowanie ich i zarządzanie nimi, ale nie tworzenie ani usuwanie kont usługi Data Lake Analytics. |
> | **Identyfikator** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | **notActions** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Usuwanie konta usługi DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Przyznaj uprawnienia, aby anulować zadania przesłane przez innych użytkowników. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Utwórz lub zaktualizuj konto usługi DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Utwórz lub zaktualizuj połączone konto DataLakeStore konta usługi DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Odłączanie konta DataLakeStore z konta usługi DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Tworzenie lub aktualizowanie konta usługi DataLakeAnalytics połączonego konta usługi Storage. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Odłączanie konta magazynu z konta usługi DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Usuwanie reguły zapory. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Utwórz lub zaktualizuj zasady obliczeń. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Usuwanie zasad obliczeń. |

## <a name="data-purger"></a>Oczyszczacz danych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może czyścić dane analizy |
> | **Identyfikator** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Akcje** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft.Insights/components/purge/action | Czyszczenie danych z usługi Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft.OperationalInsights/workspaces/purge/action | Usuń określone dane z obszaru roboczego |

## <a name="devtest-labs-user"></a>Użytkownik usługi DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w usłudze Azure DevTest Labs. |
> | **Identyfikator** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Compute/availabilitySets/read | Pobierz właściwości zestawu dostępności |
> | Microsoft.Compute/virtualMachines/*/read | Odczytywanie właściwości maszyny wirtualnej (rozmiary maszyn wirtualnych, stan czasu wykonywania, rozszerzenia maszyn wirtualnych, itp.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | Microsoft.Compute/virtualMachines/read | Pobierz właściwości maszyny wirtualnej |
> | Microsoft.Compute/virtualMachines/restart/action | Uruchamia ponownie maszynę wirtualną |
> | Microsoft.Compute/virtualMachines/start/action | Uruchamia maszynę wirtualną |
> | Microsoft.DevTestLab/*/read | Odczyt właściwości laboratorium |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Tworzenie maszyn wirtualnych w laboratorium. |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Oświadczenie losowe przejęcia maszynę wirtualną w środowisku laboratoryjnym. |
> | Microsoft.DevTestLab/labs/formulas/delete | Usunięcie formuły. |
> | Microsoft.DevTestLab/labs/formulas/read | Przeczytaj formuły. |
> | Microsoft.DevTestLab/labs/formulas/write | Dodaje lub modyfikuje formuły. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Ocenia zasady laboratorium. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Przejęcie na własność istniejącej maszyny wirtualnej |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Dołącza do puli adresów zaplecza modułu równoważenia obciążenia |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Dołącza reguły nat dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Microsoft.Network/networkInterfaces/*/read | Odczytywanie właściwości interfejsu sieciowego (na przykład wszystkie moduły równoważenia obciążenia, które interfejs sieciowy jest częścią) |
> | Microsoft.Network/networkInterfaces/join/action | Dołącza maszyny wirtualnej do interfejsu sieciowego |
> | Microsoft.Network/networkInterfaces/read | Pobiera definicja interfejsu sieciowego.  |
> | Microsoft.Network/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Microsoft.Network/publicIPAddresses/*/read | Odczytywanie właściwości publicznego adresu IP |
> | Microsoft.Network/publicIPAddresses/join/action | Dołącza publicznego adresu ip |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicji adres publiczny adres ip. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Łączy sieci wirtualnej |
> | Microsoft.Resources/deployments/operations/read | Pobiera operacje wdrażania lub wyświetla ich listę. |
> | Microsoft.Resources/deployments/read | Pobiera wdrożenia lub wyświetla ich listę. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
> | **notActions** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |

## <a name="dns-zone-contributor"></a>Współautor strefy DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie strefami DNS i zestawami rekordów w usłudze Azure DNS, ale nie zapewnia kontroli dostępu do nich. |
> | **Identyfikator** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.Network/dnsZones/* | Tworzenie i zarządzanie rekordami i strefami DNS |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="documentdb-account-contributor"></a>Współautor konta bazy danych DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Można zarządzać kontami usługi Azure Cosmos DB. Usługa Azure Cosmos DB jest znana wcześniej jako baza danych DocumentDB. |
> | **Identyfikator** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.DocumentDb/databaseAccounts/* | Tworzenie i zarządzanie kontami usługi Azure Cosmos DB |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="hdinsight-domain-services-contributor"></a>Współautor usługi HDInsight domeny
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może odczytywać powiązanych operacji tworzenia, modyfikowania i usuwania usług domenowych służące do pakiet HDInsight Enterprise Security |
> | **Identyfikator** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Akcje** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft.AAD/domainServices/*/read |  |
> | Microsoft.AAD/domainServices/oucontainer/* |  |

## <a name="intelligent-systems-account-contributor"></a>Współautor konta usługi Systemy inteligentne
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie kontami usługi Intelligent Systems, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.IntelligentSystems/accounts/* | Tworzenie i zarządzanie kontami usługi intelligent systems |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="key-vault-contributor"></a>Współautor magazynu kluczy
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie magazynami kluczy, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.KeyVault/* |  |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | **notActions** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Przeczyść usunięty nietrwale magazyn kluczy |
> | Microsoft.KeyVault/hsmPools/* |  |

## <a name="lab-creator"></a>Twórca laboratorium
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Służy do tworzenia i usuwania zarządzanych laboratoriów oraz zarządzania nimi w ramach konta usługi Azure Lab. |
> | **Identyfikator** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Tworzenie laboratorium na koncie laboratorium. |
> | Microsoft.LabServices/labAccounts/sizes/getRegionalAvailability/action |  |
> | Microsoft.LabServices/labAccounts/getRegionalAvailability/action | Uzyskaj informacje o dostępności regionalnej dla każdej kategorii rozmiar skonfigurowany w ramach konta laboratorium |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="log-analytics-contributor"></a>Współautor usługi Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Współautor usługi log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodawanie rozszerzenia do maszyn wirtualnych; Odczytywanie kluczy kont magazynu, aby można było skonfigurować zbieranie dzienników z usługi Azure Storage; Tworzenie i konfigurowanie kont usługi Automation; Dodawanie rozwiązań. i konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
> | **Identyfikator** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Akcje** |  |
> | * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla klucze dostępu dla kont magazynu. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne programu Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="log-analytics-reader"></a>Czytelnik usługi Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Czytelnik usługi Log Analytics może wyświetlać i wyszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym konfigurację diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
> | **Identyfikator** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Akcje** |  |
> | * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Wyszukaj przy użyciu nowego aparatu. |
> | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | **notActions** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Pobiera klucze wspólne dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |

## <a name="logic-app-contributor"></a>Współautor aplikacji logiki
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie aplikacjami logiki, lecz nie dostęp do nich. |
> | **Identyfikator** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla klucze dostępu dla kont magazynu. |
> | Microsoft.ClassicStorage/storageAccounts/read | Zwraca konto magazynu z podanym kontem. |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne programu Analysis Server |
> | Microsoft.Insights/logdefinitions/* | To uprawnienie jest wymagane dla użytkowników, którzy potrzebują dostępu do dzienników aktywności w portalu. Lista kategorii dziennika w dzienniku aktywności. |
> | Microsoft.Insights/metricDefinitions/* | Przeczytaj definicje metryk (lista dostępnych typów metryki dla zasobu). |
> | Microsoft.Logic/* | Zarządza zasobami Logic Apps. |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/operationresults/read | Pobiera wyniki operacji subskrypcji. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | Microsoft.Web/connectionGateways/* | Tworzenie i zarządza bramy połączenia. |
> | Microsoft.Web/connections/* | Tworzenie i zarządza nimi połączenia. |
> | Microsoft.Web/customApis/* | Tworzy i zarządza niestandardowego interfejsu API. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Pobiera właściwości planu usługi App Service |
> | Microsoft.Web/sites/functions/listSecrets/action | Listy kluczy tajnych funkcji aplikacji sieci Web. |

## <a name="logic-app-operator"></a>Operator aplikacji logiki
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczytywanie, włączanie i wyłączanie aplikacji logiki. |
> | **Identyfikator** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/*/read | Przeczytaj szczegółowe informacje, reguły alertów |
> | Microsoft.Insights/diagnosticSettings/*/read | Pobiera ustawienia diagnostyczne dla aplikacji logiki |
> | Microsoft.Insights/metricDefinitions/*/read | Pobiera dostępne metryki dla usługi Logic Apps. |
> | Microsoft.Logic/*/read | Odczytuje zasobów aplikacji logiki. |
> | Microsoft.Logic/workflows/disable/action | Wyłącza przepływ pracy. |
> | Microsoft.Logic/workflows/enable/action | Włącza przepływ pracy. |
> | Microsoft.Logic/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
> | Microsoft.Resources/deployments/operations/read | Pobiera operacje wdrażania lub wyświetla ich listę. |
> | Microsoft.Resources/subscriptions/operationresults/read | Pobiera wyniki operacji subskrypcji. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | Microsoft.Web/connectionGateways/*/read | Przeczytaj połączenia bramy. |
> | Microsoft.Web/connections/*/read | Przeczytaj połączeń. |
> | Microsoft.Web/customApis/*/read | Przeczytaj niestandardowego interfejsu API. |
> | Microsoft.Web/serverFarms/read | Pobiera właściwości planu usługi App Service |

## <a name="managed-application-operator-role"></a>Rola Operator aplikacji zarządzanej
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczytywanie i wykonywać akcje na zasobach zarządzanej aplikacji |
> | **Identyfikator** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Akcje** |  |
> | Microsoft.Solutions/applications/read | Pobiera listę aplikacji. |

## <a name="managed-applications-reader"></a>Czytnik zarządzanych aplikacji
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczyt zasobów zarządzanych aplikacji i żądanie dostępu JIT. |
> | **Identyfikator** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Akcje** |  |
> | * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Solutions/jitRequests/* |  |

## <a name="managed-identity-contributor"></a>Współautor tożsamości zarządzanych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Tworzenie, odczytywanie, aktualizowanie i usuwanie tożsamości przypisanych przez użytkownika |
> | **Identyfikator** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Akcje** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/write |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/delete |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="managed-identity-operator"></a>Operator tożsamości zarządzanych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Odczytaj i przypisz tożsamość przypisaną przez użytkownika |
> | **Identyfikator** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Akcje** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="management-group-contributor"></a>Współautor grupy zarządzania
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Rola współautora grupy zarządzania |
> | **Identyfikator** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Akcje** |  |
> | Microsoft.Management/managementGroups/delete | Usuwanie grupy zarządzania. |
> | Microsoft.Management/managementGroups/read | Wyświetlanie listy grup zarządzania dla tego uwierzytelnionego użytkownika. |
> | Microsoft.Management/managementGroups/subscriptions/delete | Kojarzy anulować subskrypcję z grupy zarządzania. |
> | Microsoft.Management/managementGroups/subscriptions/write | Kojarzy istniejącej subskrypcji z grupą zarządzania. |
> | Microsoft.Management/managementGroups/write | Utwórz lub zaktualizuj grupę zarządzania. |

## <a name="management-group-reader"></a>Czytelnik grupy zarządzania
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Rola czytelnika grupy zarządzania |
> | **Identyfikator** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Akcje** |  |
> | Microsoft.Management/managementGroups/read | Wyświetlanie listy grup zarządzania dla tego uwierzytelnionego użytkownika. |

## <a name="monitoring-contributor"></a>Współautor monitorowania
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też [Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń za pomocą usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Identyfikator** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Akcje** |  |
> | * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft.Insights/actiongroups/* |  |
> | Microsoft.Insights/AlertRules/* | Odczyt/zapis/usuwanie reguły alertu. |
> | Microsoft.Insights/components/* | Odczyt/zapis/usuwanie składników usługi Application Insights. |
> | Microsoft.Insights/DiagnosticSettings/* | Odczyt/zapis/usuwanie ustawień diagnostycznych. |
> | Microsoft.Insights/eventtypes/* | Wyświetl listę zdarzeń dziennika aktywności (zdarzenia zarządzania) w ramach subskrypcji. To uprawnienie ma zastosowanie do portalu i programowy dostęp do dziennika aktywności. |
> | Microsoft.Insights/LogDefinitions/* | To uprawnienie jest wymagane dla użytkowników, którzy potrzebują dostępu do dzienników aktywności w portalu. Lista kategorii dziennika w dzienniku aktywności. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Przeczytaj definicje metryk (lista dostępnych typów metryki dla zasobu). |
> | Microsoft.Insights/Metrics/* | Odczytać metryki dla zasobu. |
> | Microsoft.Insights/Register/Action | Zarejestruj dostawcę Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Testy sieci web usługi Application Insights odczytu/zapisu/usuwania. |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Odczyt/zapis/usuwanie pakietów rozwiązań usługi Log Analytics. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Odczyt/zapis/usuwanie Log Analytics zapisane wyszukiwania. |
> | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Pobiera klucze wspólne dla obszaru roboczego. Te klucze są używane do łączenia agentów usługi Microsoft Operational Insights z obszarem roboczym. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Konfiguracje szczegółowe informacje o odczytu/zapisu/usuwanie magazynu usługi Log Analytics. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft.WorkloadMonitor/workloadInsights/* |  |

## <a name="monitoring-metrics-publisher"></a>Monitorowanie metryk wydawcy
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia publikowanie metryk dotyczących zasobów platformy Azure |
> | **Identyfikator** | 3913510d-42f4-4E42-8a64-420c390055eb |
> | **Akcje** |  |
> | Microsoft.Insights/Register/Action | Zarejestruj dostawcę Microsoft Insights |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | **Elementy DataActions** |  |
> | Microsoft.Insights/Metrics/Write | Metryki zapisu |

## <a name="monitoring-reader"></a>Czytelnik monitorowania
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może odczytywać wszystkie dane monitorowania (metryk, dzienników itp.). Zobacz też [Rozpoczynanie pracy z rolami, uprawnienia i zabezpieczeń za pomocą usługi Azure Monitor](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles). |
> | **Identyfikator** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Akcje** |  |
> | * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="network-contributor"></a>Współautor sieci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie sieciami, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.Network/* | Tworzenie i zarządzanie sieciami |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="new-relic-apm-account-contributor"></a>Współautor konta usługi New Relic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie kontami i aplikacjami usługi New Relic Application Performance Management, ale nie umożliwia uzyskiwania dostępu do nich. |
> | **Identyfikator** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | NewRelic.APM/accounts/* |  |

## <a name="reader-and-data-access"></a>Czytnik i dostęp do danych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Pozwala przeglądać wszystko, ale nie będzie można usunąć lub utworzyć konto magazynu lub zawartego zasobu. Będzie również umożliwiać dostępu odczytu i zapisu do wszystkich danych znajdujących się na koncie magazynu za pośrednictwem dostępu do kluczy konta magazynu. |
> | **Identyfikator** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Akcje** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |

## <a name="redis-cache-contributor"></a>Współautor pamięci podręcznej Redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie pamięciami podręcznymi Redis, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Cache/redis/* | Tworzenie i zarządzanie pamięciami podręcznymi redis Cache |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="resource-policy-contributor-preview"></a>Współautor zasad zasobów (wersja zapoznawcza)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | (Wersja zapoznawcza) Użytkownicy uzupełnieni z umowy EA z prawami do tworzenia/modyfikowania zasad zasobów, tworzenia biletów pomocy technicznej i odczytywania zasobów/hierarchii. |
> | **Identyfikator** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Akcje** |  |
> | * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.Authorization/policyassignments/* | Tworzenie i zarządzanie nimi przypisania zasad |
> | Microsoft.Authorization/policydefinitions/* | Tworzenie i zarządzanie nimi definicji zasad |
> | Microsoft.Authorization/policysetdefinitions/* | Tworzenie i zarządzanie nimi zestawów zasad |
> | Microsoft.PolicyInsights/* |  |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="scheduler-job-collections-contributor"></a>Współautor kolekcji zadań usługi Harmonogram
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie kolekcjami zadań usługi Scheduler, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Scheduler/jobcollections/* | Tworzenie i zarządzanie nimi kolekcji zadań |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="search-service-contributor"></a>Współautor usługi wyszukiwania
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie usługami Search, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Search/searchServices/* | Tworzenie i zarządzanie nimi usługi wyszukiwania |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="security-admin"></a>Administrator zabezpieczeń
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | W usłudze Security Center tylko: można wyświetlić zasady zabezpieczeń, widoku stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlanie alertów i zaleceń, Odrzuć alerty i zalecenia |
> | **Identyfikator** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Authorization/policyAssignments/* | Tworzenie i zarządzanie nimi przypisania zasad |
> | Microsoft.Authorization/policyDefinitions/* | Tworzenie i zarządzanie nimi definicji zasad |
> | Microsoft.Authorization/policySetDefinitions/* | Tworzenie i zarządzanie nimi zestawów zasad |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.Management/managementGroups/read | Wyświetlanie listy grup zarządzania dla tego uwierzytelnionego użytkownika. |
> | Microsoft.operationalInsights/workspaces/*/read | Wyświetl dane usługi Log Analytics |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Security/*/read | Składniki zabezpieczeń Odczytaj i zasady |
> | Microsoft.Security/locations/alerts/activate/action | Uaktywnij alert zabezpieczeń |
> | Microsoft.Security/locations/alerts/dismiss/action | Odrzuć alert zabezpieczeń |
> | Microsoft.Security/locations/tasks/activate/action | Aktywuj zalecenie dotyczące zabezpieczeń |
> | Microsoft.Security/locations/tasks/dismiss/action | Odrzuć zalecenie dotyczące zabezpieczeń |
> | Microsoft.Security/policies/write | Aktualizuje zasady zabezpieczeń |
> | Microsoft.Security/securityContacts/write | Aktualizacje zabezpieczeń skontaktuj się z |
> | Microsoft.Security/securityContacts/delete | Usuwa kontakt zabezpieczeń |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="security-manager"></a>Menedżer zabezpieczeń
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie składnikami zabezpieczeń, zasadami zabezpieczeń i maszynami wirtualnymi |
> | **Identyfikator** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.ClassicCompute/*/read | Przeczytaj informacje o konfiguracji klasycznych maszyn wirtualnych |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Zapisz konfigurację dla klasycznych maszyn wirtualnych |
> | Microsoft.ClassicNetwork/*/read | Przeczytaj informacje o konfiguracji dotyczące klasycznej sieci |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Security/* | Tworzenie i zarządzanie nimi składniki zabezpieczeń i zasad |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="security-reader"></a>Odczytywanie zabezpieczeń
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | W usłudze Security Center tylko: mogą wyświetlać, zaleceń i alertów, widok zasady zabezpieczeń, wyświetlanie stanów zabezpieczeń, ale nie może wprowadzać zmian |
> | **Identyfikator** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.operationalInsights/workspaces/*/read | Wyświetl dane usługi Log Analytics |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Security/*/read | Składniki zabezpieczeń Odczytaj i zasady |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | Microsoft.Management/managementGroups/read | Wyświetlanie listy grup zarządzania dla tego uwierzytelnionego użytkownika. |

## <a name="site-recovery-contributor"></a>Współautor usługi Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Pozwala zarządzać usługą Site Recovery z wyjątkiem tworzenia magazynu i przypisywania ról |
> | **Identyfikator** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest wewnętrzną operacją używaną przez usługę |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Tworzenie i zarządzanie nimi rozszerzone informacje związane z magazynu |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "Magazyn" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Tworzenie i Zarządzanie tożsamościami zarejestrowane |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Utwórz lub zaktualizuj ustawienia alertu replikacji |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Odczyt zdarzeń |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Tworzenie i zarządzanie nimi w sieci szkieletowej replikacji |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Tworzenie i zarządzanie nimi zadania replikacji |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Tworzenie i zarządzanie zasadami replikacji |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Tworzenie i zarządzanie planami recovery plan |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Tworzenie i zarządzanie nimi konfiguracji magazynu magazynu usług Recovery Services |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Zwraca informacje o tokenie dla magazynu usługi Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja Token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Odczyt alertów dla magazynu usługi Recovery services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="site-recovery-operator"></a>Operator usługi Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Pozwala przechodzić do trybu failover i przywracać sprawność po awarii, ale nie umożliwia wykonywania innych operacji zarządzania usługi Site Recovery |
> | **Identyfikator** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest wewnętrzną operacją używaną przez usługę |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "Magazyn" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyników operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Pobierz kontenery operacji umożliwia pobranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Odczytuje żadnych ustawień alertów |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Odczyt zdarzeń |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Sprawdza spójność sieci szkieletowej |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Przeczytaj żadnych sieci szkieletowych |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Skojarz ponownie bramę |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Odnów certyfikat sieci szkieletowej |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Odczyt wszystkich sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytywać wszystkie mapowania sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Odczytywać wszystkie kontenery ochrony |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Odczyt wszystkich elementów podlegających ochronie |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Zastosuj punkt odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Zatwierdzenie trybu failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planowany tryb Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Odczyt wszystkich chronionych elementów |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytywać wszystkie punkty odzyskiwania replikacji |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Napraw replikację |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Włącz ponownie ochronę chronionego elementu |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Test pracy w trybie failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Czyszczenie testowego przełączania do trybu Failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Tryb failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizacja usługi mobilności |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytywać wszystkie mapowania kontenera ochrony |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Odczyt wszystkich dostawców usług odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Odśwież dostawcę |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytywać wszystkie klasyfikacje magazynów |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytywać wszystkie mapowania klasyfikacji magazynów |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Przeczytaj wszelkie vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Tworzenie i zarządzanie nimi zadania replikacji |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Odczytywać wszystkie zasady |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan odzyskiwania zatwierdzania trybu failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan planowanego trybu Failover odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Odczyt wszystkich planów odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Ponownie włączyć ochronę planu odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plan odzyskiwania do trybu Failover testu |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan odzyskiwania systemu testowego przełączania w tryb Failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan odzyskiwania do trybu failover |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Odczyt alertów dla magazynu usługi Recovery services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Zwraca informacje o tokenie dla magazynu usługi Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja Token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="site-recovery-reader"></a>Czytelnik usługi Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Pozwala wyświetlać stan usługi Site Recovery, ale nie umożliwia wykonywania innych operacji zarządzania |
> | **Identyfikator** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "Magazyn" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyników operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Pobierz kontenery operacji umożliwia pobranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Odczytuje żadnych ustawień alertów |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Odczyt zdarzeń |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Przeczytaj żadnych sieci szkieletowych |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Odczyt wszystkich sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytywać wszystkie mapowania sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Odczytywać wszystkie kontenery ochrony |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Odczyt wszystkich elementów podlegających ochronie |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Odczyt wszystkich chronionych elementów |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytywać wszystkie punkty odzyskiwania replikacji |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytywać wszystkie mapowania kontenera ochrony |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Odczyt wszystkich dostawców usług odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytywać wszystkie klasyfikacje magazynów |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytywać wszystkie mapowania klasyfikacji magazynów |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Przeczytaj wszelkie vCenters |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Odczytać wszystkie zadania |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Odczytywać wszystkie zasady |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Odczyt wszystkich planów odzyskiwania |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read | Zwraca informacje o tokenie dla magazynu usługi Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja Token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="sql-db-contributor"></a>Współautor bazy danych SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie bazami danych SQL, ale nie umożliwia uzyskiwania dostępu do nich. Ponadto nie można zarządzać ich zasad związanych z bezpieczeństwem ani nadrzędnymi serwerami SQL. |
> | **Identyfikator** | 9b7fa17d-e63e-47B0-bb0a-15c516ac86ec |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Tworzenie i zarządzanie nimi baz danych SQL |
> | Microsoft.Sql/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | Microsoft.Insights/metrics/read | Odczytaj metryki |
> | Microsoft.Insights/metricDefinitions/read | Przeczytaj definicje metryk |
> | **notActions** |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Nie można edytować zasad inspekcji |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Nie można edytować ustawienia inspekcji |
> | Microsoft.Sql/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów blob bazy danych |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Nie można edytować zasad połączenia |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Nie można edytować zasad maskowania danych |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Nie można edytować zasady alertu zabezpieczeń |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Nie można edytować metryki zabezpieczeń |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |

## <a name="sql-security-manager"></a>Menedżer zabezpieczeń SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie zasadami serwerów i baz danych SQL związanymi z zabezpieczeniami, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Autoryzacja Microsoft odczytu |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza do zasobów, takich jak konto magazynu lub bazy danych SQL do podsieci. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Tworzenie i zarządzanie nimi zasady inspekcji usługi SQL server |
> | Microsoft.Sql/servers/auditingSettings/* | Tworzenie i zarządzanie nimi ustawienia inspekcji serwera SQL |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Tworzenie i zarządzanie nimi zasady inspekcji bazy danych programu SQL server |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Tworzenie i zarządzanie nimi ustawienia inspekcji bazy danych programu SQL server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Odczytaj rekordy inspekcji |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Tworzenie i zarządzanie zasadami połączeń bazy danych serwera SQL |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Tworzenie i zarządzanie nimi danych bazy danych programu SQL server maskowania zasad |
> | Microsoft.Sql/servers/databases/read | Zwraca listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | Microsoft.Sql/servers/databases/schemas/read | Pobieranie listy schematy bazy danych |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Pobieranie listy kolumn w tabeli |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Pobieranie listy tabel bazy danych |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Tworzenie i zarządzanie zasadami alertów zabezpieczeń bazy danych serwera SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Tworzenie i zarządzanie nimi metryk zabezpieczeń bazy danych serwera SQL |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Zwraca listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Tworzenie i zarządzanie zasadami alertów zabezpieczeń serwera SQL |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="sql-server-contributor"></a>Współautor serwera SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie serwerami i bazami danych SQL, ale nie umożliwia uzyskiwania dostępu do nich ani do ich zasad związanych z zabezpieczeniami. |
> | **Identyfikator** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Tworzenie i zarządzanie serwerami programu SQL Server |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | Microsoft.Insights/metrics/read | Odczytaj metryki |
> | Microsoft.Insights/metricDefinitions/read | Przeczytaj definicje metryk |
> | **notActions** |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Nie można edytować zasady inspekcji usługi SQL server |
> | Microsoft.Sql/servers/auditingSettings/* | Nie można edytować ustawień inspekcji serwera SQL |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Nie można edytować zasady inspekcji bazy danych programu SQL server |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Nie można edytować ustawienia inspekcji bazy danych programu SQL server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Nie można odczytać rekordów inspekcji |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Nie można edytować zasad połączenia bazy danych serwera SQL |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Nie można edytować danych bazy danych programu SQL server maskowania zasad |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Nie można edytować zasad alertów zabezpieczeń bazy danych serwera SQL |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Nie można edytować metryki zabezpieczeń bazy danych programu SQL server |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Nie można edytować zasady alertu zabezpieczeń serwera SQL |

## <a name="storage-account-contributor"></a>Współautor konta magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie kontami magazynu, ale nie dostęp do nich. |
> | **Identyfikator** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczyt wszystkich autoryzacji |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Insights/diagnosticSettings/* | Zarządzanie ustawień diagnostycznych |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza do zasobów, takich jak konto magazynu lub bazy danych SQL do podsieci. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Storage/storageAccounts/* | Tworzenie i zarządzanie kontami magazynu |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="storage-account-key-operator-service-role"></a>Rola usługi Operator kluczy kont magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Operatorzy kluczy kont magazynu mogą wyświetlać listę kluczy dla kont magazynu i ponownie je generować |
> | **Identyfikator** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Akcje** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Generuje ponownie klucze dostępu dla podanego konta magazynu. |

## <a name="storage-blob-data-contributor-preview"></a>Współautor danych obiektu blob magazynu (wersja zapoznawcza)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na dostęp do odczytu, zapisu i usuwania do kontenerów obiektów blob i danych usługi Azure Storage |
> | **Identyfikator** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Akcje** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/delete | Zwraca wynik usunięcia kontenera |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Zwraca kontener lub listę kontenerów |
> | Microsoft.Storage/storageAccounts/blobServices/containers/write | Zwraca wynik umieszczania lub dzierżawienia kontenera obiektów blob |
> | **Elementy DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete | Zwraca wynik usunięcia obiektu blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Zwraca obiekt blob lub listę obiektów blob |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write | Zwraca wynik zapisania obiektu blob |

## <a name="storage-blob-data-reader-preview"></a>Czytnik danych obiektu blob magazynu (wersja zapoznawcza)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na dostęp do odczytu do kontenerów obiektów blob i danych usługi Azure Storage |
> | **Identyfikator** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Akcje** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/read | Zwraca kontener lub listę kontenerów |
> | **Elementy DataActions** |  |
> | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read | Zwraca obiekt blob lub listę obiektów blob |

## <a name="storage-queue-data-contributor-preview"></a>Współautor danych kolejki magazynu (wersja zapoznawcza)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na dostęp do odczytu, zapisu i usuwania do kolejek i komunikatów kolejek usługi Azure Storage |
> | **Identyfikator** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Akcje** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/delete | Zwraca wynik usunięcia kolejki |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Zwraca kolejkę lub listę kolejek. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/write | Zwraca wynik zapisania kolejki |
> | **Elementy DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/delete | Zwraca wynik usunięcia komunikatu |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Zwraca komunikat |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/write | Zwraca wynik zapisania komunikatu |

## <a name="storage-queue-data-reader-preview"></a>Czytnik danych kolejki magazynu (wersja zapoznawcza)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na dostęp do odczytu do kolejek i komunikatów kolejek usługi Azure Storage |
> | **Identyfikator** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Akcje** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Zwraca kolejkę lub listę kolejek. |
> | **Elementy DataActions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Zwraca komunikat |

## <a name="support-request-contributor"></a>Współautor żądania pomocy technicznej
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia tworzenie żądań pomocy technicznej i zarządzanie nimi |
> | **Identyfikator** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="traffic-manager-contributor"></a>Współautor usługi Traffic Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie profilami usługi Traffic Manager, ale nie zapewnia kontroli dostępu do nich. |
> | **Identyfikator** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj ról i przypisań ról |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="user-access-administrator"></a>Administrator dostępu użytkowników
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure. |
> | **Identyfikator** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Akcje** |  |
> | * / Odczyt | Przeczytaj zasoby wszystkich typów, z wyjątkiem wpisów tajnych. |
> | Microsoft.Authorization/* | Zarządzanie autoryzacji |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="virtual-machine-administrator-login"></a>Logowanie administratora maszyny wirtualnej
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Wyświetl maszyny wirtualne w portalu i zaloguj się jako administrator |
> | **Identyfikator** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Akcje** |  |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicji adres publiczny adres ip. |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.Network/loadBalancers/read | Pobiera definicji modułu równoważenia obciążenia |
> | Microsoft.Network/networkInterfaces/read | Pobiera definicja interfejsu sieciowego.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **Elementy DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Zaloguj się do maszyny wirtualnej z uprawnieniami administratora systemu Windows lub użytkownika root systemu Linux |

## <a name="virtual-machine-contributor"></a>Współautor maszyny wirtualnej
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie maszynami wirtualnymi, ale nie dostęp do nich ani do sieci wirtualnych i konta magazynu, z którymi są połączone. |
> | **Identyfikator** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
> | Microsoft.Compute/availabilitySets/* | Utwórz i Zarządzaj zbiorami dostępności obliczeń |
> | Microsoft.Compute/locations/* | Tworzenie i zarządzanie nimi lokalizacje obliczeń |
> | Microsoft.Compute/virtualMachines/* | Tworzenie i zarządzanie maszynami wirtualnymi |
> | Microsoft.Compute/virtualMachineScaleSets/* | Tworzenie i zarządzanie nimi w zestawach skalowania maszyn wirtualnych |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Dołącza do puli adresów zaplecza bramy aplikacji |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Dołącza do puli adresów zaplecza modułu równoważenia obciążenia |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Dołącza modułu równoważenia obciążenia dla ruchu przychodzącego translatora adresów sieciowych puli |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Dołącza reguły nat dla ruchu przychodzącego modułu równoważenia obciążenia |
> | Microsoft.Network/loadBalancers/probes/join/action | Umożliwia użycie sond modułu równoważenia obciążenia. Na przykład z tą właściwością healthProbe uprawnień dla usługi VM scale set odwoływać się do sondy. |
> | Microsoft.Network/loadBalancers/read | Pobiera definicji modułu równoważenia obciążenia |
> | Microsoft.Network/locations/* | Tworzenie i zarządzanie nimi lokalizacje sieciowe |
> | Microsoft.Network/networkInterfaces/* | Tworzenie i zarządzanie interfejsami sieciowymi |
> | Microsoft.Network/networkSecurityGroups/join/action | Dołącza sieciowej grupy zabezpieczeń |
> | Microsoft.Network/networkSecurityGroups/read | Pobiera definicję grupy zabezpieczeń sieci |
> | Microsoft.Network/publicIPAddresses/join/action | Dołącza publicznego adresu ip |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicji adres publiczny adres ip. |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Łączy sieci wirtualnej |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Utwórz opcję ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Utwórz chroniony element kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Tworzy zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "Magazyn" |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn” |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Zwraca klucze dostępu dla podanego konta magazynu. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu bądź pobiera właściwości dla podanego konta magazynu. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |

## <a name="virtual-machine-user-login"></a>Logowanie użytkownika maszyny wirtualnej
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Wyświetl maszyny wirtualne w portalu i zaloguj się jako zwykli użytkownicy. |
> | **Identyfikator** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Akcje** |  |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicji adres publiczny adres ip. |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.Network/loadBalancers/read | Pobiera definicji modułu równoważenia obciążenia |
> | Microsoft.Network/networkInterfaces/read | Pobiera definicja interfejsu sieciowego.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **Elementy DataActions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |

## <a name="web-plan-contributor"></a>Współautor planów sieci Web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie planami sieci Web dla witryn sieci Web, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | Microsoft.Web/serverFarms/* | Tworzenie i zarządzanie nimi farmy serwerów |

## <a name="website-contributor"></a>Współautor witryny sieci Web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie witrynami sieci Web (nie planami sieci Web), ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Przeczytaj autoryzacji |
> | Microsoft.Insights/alertRules/* | Tworzenie i zarządzanie nimi reguły alertów usługi Insights |
> | Microsoft.Insights/components/* | Tworzenie i zarządzanie składnikami usługi Insights |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stany dostępności wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/* | Tworzenie i zarządzanie wdrożeniami grup zasobów |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera grupy zasobów lub wyświetla ich listę. |
> | Microsoft.Support/* | Tworzenie i zarządzanie biletami pomocy technicznej |
> | Microsoft.Web/certificates/* | Tworzenie i zarządzanie certyfikatami witryny sieci Web |
> | Microsoft.Web/listSitesAssignedToHostName/read | Pobierz nazwy lokacji przypisany do nazwy hosta. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Pobiera właściwości planu usługi App Service |
> | Microsoft.Web/sites/* | Tworzenie i zarządzanie witrynami sieci Web (Tworzenie witryn wymaga także uprawnienia do zapisu do skojarzonego planu usługi App Service) |

## <a name="next-steps"></a>Kolejne kroki

- [Role niestandardowe](custom-roles.md)
- [Zarządzanie przypisaniami ról przy użyciu witryny Azure portal](role-assignments-portal.md)
- [Uprawnienia w usłudze Azure Security Center](../security-center/security-center-permissions.md)
