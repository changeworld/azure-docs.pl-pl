---
title: Wbudowane role dla zasobów platformy Azure | Microsoft Docs
description: Opisuje wbudowane role kontroli dostępu opartej na rolach (RBAC) i zasobów platformy Azure. Wyświetla listę akcji, nienaruszonych, akcji i NotDataActions.
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
ms.date: 08/27/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: it-pro
ms.openlocfilehash: c2066502a6a9d742c347e08d528a2490a390bb7e
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2019
ms.locfileid: "71672647"
---
# <a name="built-in-roles-for-azure-resources"></a>Wbudowane role dla zasobów platformy Azure

[Kontrola dostępu oparta na rolach (RBAC)](overview.md) ma kilka wbudowanych ról dla zasobów platformy Azure, które można przypisać do użytkowników, grup, podmiotów usługi i zarządzanych tożsamości. Przypisania ról są sposobem kontroli dostępu do zasobów platformy Azure. Jeśli role wbudowane nie spełniają potrzeb Twojej organizacji, możesz tworzyć własne [role niestandardowe dla zasobów platformy Azure](custom-roles.md).

W tym artykule wymieniono wbudowane role dla zasobów platformy Azure, które są zawsze zmieniane. Aby uzyskać najnowsze role, użyj funkcji [Get-AzRoleDefinition](/powershell/module/az.resources/get-azroledefinition) lub [AZ role Definition list](/cli/azure/role/definition#az-role-definition-list). Jeśli szukasz ról administratorów dla Azure Active Directory, zobacz [uprawnienia roli administratora w programie Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

## <a name="built-in-role-descriptions"></a>Wbudowane opisy ról

W poniższej tabeli przedstawiono krótki opis każdej wbudowanej roli. Kliknij nazwę roli `Actions`, aby wyświetlić listę, `NotActions`, `DataActions`i `NotDataActions` dla każdej roli. Aby uzyskać informacje o tym, co oznaczają te działania oraz o sposobie ich zastosowania do płaszczyzny zarządzania i danych, zobacz [Omówienie definicji ról dla zasobów platformy Azure](role-definitions.md).


| Wbudowana rola | Opis |
| --- | --- |
| [Właściciel](#owner) | Umożliwia zarządzanie wszystko, w tym dostęp do zasobów. |
| [Współautor](#contributor) | Umożliwia zarządzanie wszystko z wyjątkiem udzielania dostępu do zasobów. |
| [Czytelnik](#reader) | Umożliwia wyświetlenie wszystkiego, ale nie wprowadza żadnych zmian. |
| [AcrDelete](#acrdelete) | usuwanie rekordu acr |
| [AcrImageSigner](#acrimagesigner) | osoba podpisująca obraz ACR |
| [AcrPull](#acrpull) | acr pull |
| [AcrPush](#acrpush) | acr push |
| [AcrQuarantineReader](#acrquarantinereader) | czytnik danych kwarantanny ACR |
| [AcrQuarantineWriter](#acrquarantinewriter) | moduł zapisywania danych kwarantanny ACR |
| [Współautor usługi API Management](#api-management-service-contributor) | Może zarządzać usługą i interfejsami API |
| [Rola operatora usługi API Management](#api-management-service-operator-role) | Może zarządzać usługą, ale nie z interfejsów API |
| [Rola czytnika usługi API Management](#api-management-service-reader-role) | Dostęp tylko do odczytu do usługi i interfejsów API |
| [Współautor składnika Application Insights](#application-insights-component-contributor) | Umożliwia zarządzanie składnikami usługi Application Insights |
| [Application Insights Snapshot Debugger](#application-insights-snapshot-debugger) | Daje użytkownikowi uprawnienia do wyświetlania i pobierania migawek debugowania zebranych za pomocą Snapshot Debugger Application Insights. Należy zauważyć, że te uprawnienia nie są uwzględnione w rolach [właściciel](#owner) lub [współautor](#contributor) . |
| [Operator zadania automatyzacji](#automation-job-operator) | Twórz zadania i zarządzaj nimi za pomocą elementów Runbook usługi Automation. |
| [Operator automatyzacji](#automation-operator) | Operatory automatyzacji mogą uruchamiać, zatrzymywać, wstrzymywać i wznawiać zadania |
| [Operator elementu Runbook usługi Automation](#automation-runbook-operator) | Odczytywanie właściwości elementu Runbook — aby można było tworzyć zadania elementu Runbook. |
| [Współautor avere](#avere-contributor) | Można utworzyć klaster avere vFXT i zarządzać nim. |
| [Operator avere](#avere-operator) | Używane przez klaster avere vFXT do zarządzania klastrem |
| [Właściciel danych Event Hubs platformy Azure](#azure-event-hubs-data-owner) | Umożliwia pełny dostęp do zasobów usługi Azure Event Hubs. |
| [Usługa Azure Event Hubs Data Receiver](#azure-event-hubs-data-receiver) | Zezwala na odbieranie dostępu do zasobów usługi Azure Event Hubs. |
| [Nadawca danych Event Hubs platformy Azure](#azure-event-hubs-data-sender) | Zezwala na wysyłanie dostępu do zasobów Event Hubs platformy Azure. |
| [Rola administratora klastra usługi Kubernetes platformy Azure](#azure-kubernetes-service-cluster-admin-role) | Wyświetl listę akcji poświadczeń administratora klastra. |
| [Rola użytkownika klastra usługi Azure Kubernetes Service](#azure-kubernetes-service-cluster-user-role) | Wyświetl listę akcji poświadczeń użytkownika klastra. |
| [Czytnik danych Azure Maps (wersja zapoznawcza)](#azure-maps-data-reader-preview) | Przyznaje dostęp do odczytu danych związanych z mapowaniem z konta usługi Azure Maps. |
| [Właściciel danych Azure Service Bus](#azure-service-bus-data-owner) | Umożliwia pełny dostęp do zasobów Azure Service Bus. |
| [Azure Service Bus odbiorca danych](#azure-service-bus-data-receiver) | Umożliwia uzyskanie dostępu do zasobów Azure Service Bus. |
| [Nadawca danych Azure Service Bus](#azure-service-bus-data-sender) | Zezwala na dostęp do Azure Service Bus zasobów. |
| [Właściciel rejestracji Azure Stack](#azure-stack-registration-owner) | Umożliwia zarządzanie rejestracjami Azure Stack. |
| [Współautor kopii zapasowej](#backup-contributor) | Umożliwia zarządzanie usługą kopii zapasowych, ale nie może tworzyć magazynów i zapewniać dostępu innym osobom |
| [Operator kopii zapasowych](#backup-operator) | Umożliwia zarządzanie usługami kopii zapasowych, z wyjątkiem usuwania kopii zapasowych, tworzenia magazynu i udzielania dostępu innym osobom |
| [Czytnik kopii zapasowych](#backup-reader) | Może wyświetlać usługi kopii zapasowej, ale nie może wprowadzać zmian |
| [Czytnik rozliczeń](#billing-reader) | Zezwala na dostęp do odczytu do danych rozliczeniowych |
| [BizTalk Contributor](#biztalk-contributor) | Umożliwia zarządzanie usługami BizTalk Services, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Dostęp do węzła elementu członkowskiego łańcucha bloków (wersja zapoznawcza)](#blockchain-member-node-access-preview) | Zezwala na dostęp do węzłów składowych łańcucha bloków |
| [Współautor planu](#blueprint-contributor) | Może zarządzać definicjami planu, ale nie należy ich przypisywać. |
| [Operator planu](#blueprint-operator) | Może przypisywać istniejące opublikowane plany, ale nie może tworzyć nowych planów. Uwaga: to działanie działa tylko wtedy, gdy przypisanie zostało wykonane przy użyciu tożsamości zarządzanej przypisanej przez użytkownika. |
| [Współautor punktu końcowego usługi CDN](#cdn-endpoint-contributor) | Może zarządzać punktami końcowymi usługi CDN, ale nie może udzielić dostępu innym użytkownikom. |
| [Czytnik punktu końcowego usługi CDN](#cdn-endpoint-reader) | Może wyświetlać punkty końcowe usługi CDN, ale nie może wprowadzać zmian. |
| [Współautor profilu CDN](#cdn-profile-contributor) | Może zarządzać profilami sieci CDN i ich punktami końcowymi, ale nie może udzielać dostępu innym użytkownikom. |
| [Czytnik profilu CDN](#cdn-profile-reader) | Może wyświetlać profile usługi CDN i ich punkty końcowe, ale nie może wprowadzać zmian. |
| [Współautor klasycznej sieci](#classic-network-contributor) | Umożliwia zarządzanie sieciami klasycznymi, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor klasycznego konta magazynu](#classic-storage-account-contributor) | Umożliwia zarządzanie klasycznymi kontami magazynu, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Rola usługi operatora kluczy klasycznego konta magazynu](#classic-storage-account-key-operator-service-role) | Operatorzy kluczy klasycznego konta magazynu mogą wyświetlać i ponownie generować klucze na klasycznych kontach magazynu |
| [Współautor klasycznej maszyny wirtualnej](#classic-virtual-machine-contributor) | Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie umożliwia uzyskiwania do nich dostępu, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone. |
| [Współautor Cognitive Services](#cognitive-services-contributor) | Umożliwia tworzenie, odczytywanie, aktualizowanie, usuwanie i zarządzanie kluczami Cognitive Services. |
| [Czytnik danych Cognitive Services (wersja zapoznawcza)](#cognitive-services-data-reader-preview) | Umożliwia odczytywanie danych Cognitive Services. |
| [Cognitive Services użytkownika](#cognitive-services-user) | Umożliwia odczytywanie i wyświetlanie listy kluczy Cognitive Services. |
| [Rola czytnika konta Cosmos DB](#cosmos-db-account-reader-role) | Może odczytywać Azure Cosmos DB dane konta. Aby zarządzać kontami Azure Cosmos DB, zobacz [współautor konta usługi DocumentDB](#documentdb-account-contributor) . |
| [Operator Cosmos DB](#cosmos-db-operator) | Umożliwia zarządzanie kontami Azure Cosmos DB, ale nie dostęp do danych w nich. Uniemożliwia dostęp do kluczy konta i parametrów połączenia. |
| [CosmosBackupOperator](#cosmosbackupoperator) | Może przesłać żądanie przywracania dla bazy danych Cosmos DB lub kontenera dla konta |
| [Współautor Cost Management](#cost-management-contributor) | Umożliwia wyświetlanie kosztów i zarządzanie konfiguracją kosztów (np. budżetów, eksportów) |
| [Cost Management czytelnik](#cost-management-reader) | Może wyświetlać dane i konfigurację kosztów (np. budżetów, eksportów) |
| [Współautor urządzenie Data Box](#data-box-contributor) | Umożliwia zarządzanie wszystko w obszarze usługi urządzenie Data Box z wyjątkiem udzielenia dostępu innym osobom. |
| [urządzenie Data Box czytelnik](#data-box-reader) | Umożliwia zarządzanie usługą urządzenie Data Box, z wyjątkiem tworzenia kolejności lub edytowania szczegółów kolejności i udzielania dostępu innym osobom. |
| [Współautor Data Factory](#data-factory-contributor) | Twórz fabryki danych i zarządzaj nimi, a także zasobami podrzędnymi. |
| [Data Lake Analytics deweloper](#data-lake-analytics-developer) | Umożliwia przesyłanie własnych zadań, monitorowanie ich i zarządzanie nimi, ale nie tworzenie ani usuwanie kont Data Lake Analytics. |
| [Przeczyszczanie danych](#data-purger) | Może czyścić dane analizy |
| [Użytkownik DevTest Labs](#devtest-labs-user) | Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w Azure DevTest Labs. |
| [Współautor strefy DNS](#dns-zone-contributor) | Umożliwia zarządzanie strefami i zestawami rekordów DNS w Azure DNS, ale nie pozwala na kontrolowanie dostępu do nich. |
| [Współautor konta DocumentDB](#documentdb-account-contributor) | Może zarządzać kontami Azure Cosmos DB. Azure Cosmos DB jest dawniej znany jako DocumentDB. |
| [Współautor EventGrid EventSubscription](#eventgrid-eventsubscription-contributor) | Umożliwia zarządzanie operacjami subskrypcji zdarzeń EventGrid. |
| [EventGrid EventSubscription](#eventgrid-eventsubscription-reader) | Umożliwia odczytanie subskrypcji zdarzeń EventGrid. |
| [Operator klastra usługi HDInsight](#hdinsight-cluster-operator) | Umożliwia odczytywanie i modyfikowanie konfiguracji klastrów usługi HDInsight. |
| [Współautor usług domenowych w usłudze HDInsight](#hdinsight-domain-services-contributor) | Może odczytywać, tworzyć, modyfikować i usuwać operacje związane z usługami domenowymi, które są potrzebne pakiet Enterprise Security usługi HDInsight |
| [Współautor konta systemów inteligentnych](#intelligent-systems-account-contributor) | Umożliwia zarządzanie kontami inteligentnych systemów, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor Key Vault](#key-vault-contributor) | Umożliwia Zarządzanie magazynami kluczy, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Twórca laboratorium](#lab-creator) | Umożliwia tworzenie i usuwanie zarządzanych laboratoriów z kont laboratorium platformy Azure oraz zarządzanie nimi. |
| [Współautor Log Analytics](#log-analytics-contributor) | Współautor Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; Odczytywanie kluczy konta magazynu w celu skonfigurowania kolekcji dzienników z usługi Azure Storage; Tworzenie i Konfigurowanie kont usługi Automation; Dodawanie rozwiązań; i Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
| [Log Analytics czytelnik](#log-analytics-reader) | Log Analytics Reader może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym Wyświetlanie konfiguracji diagnostyki platformy Azure na wszystkich zasobach platformy Azure. |
| [Współautor aplikacji logiki](#logic-app-contributor) | Umożliwia zarządzanie aplikacjami logiki, ale nie umożliwia zmiany dostępu do nich. |
| [Operator aplikacji logiki](#logic-app-operator) | Umożliwia odczytywanie, Włączanie i wyłączanie aplikacji logiki, ale nie ich edytowanie ani aktualizowanie. |
| [Rola operatora aplikacji zarządzanej](#managed-application-operator-role) | Umożliwia odczytywanie i wykonywanie akcji dotyczących zasobów aplikacji zarządzanej |
| [Czytnik aplikacji zarządzanych](#managed-applications-reader) | Umożliwia odczytanie zasobów w zarządzanej aplikacji i zażądanie dostępu JIT. |
| [Współautor tożsamości zarządzanej](#managed-identity-contributor) | Tworzenie, odczytywanie, aktualizowanie i usuwanie tożsamości przypisanej przez użytkownika |
| [Operator tożsamości zarządzanej](#managed-identity-operator) | Odczytaj i przypisz tożsamość przypisaną przez użytkownika |
| [Współautor grupy zarządzania](#management-group-contributor) | Rola współautora grupy zarządzania |
| [Czytelnik grupy zarządzania](#management-group-reader) | Rola czytelnika grupy zarządzania |
| [Współautor monitorowania](#monitoring-contributor) | Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Wydawca metryk monitorowania](#monitoring-metrics-publisher) | Włącza publikowanie metryk dla zasobów platformy Azure |
| [Czytnik monitorowania](#monitoring-reader) | Może odczytywać wszystkie dane monitorowania (metryki, dzienniki itp.). Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
| [Współautor sieci](#network-contributor) | Umożliwia zarządzanie sieciami, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Nowe współautor konta APM Relic](#new-relic-apm-account-contributor) | Umożliwia zarządzanie kontami i aplikacjami New Relic Application Performance Management, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Czytnik i dostęp do danych](#reader-and-data-access) | Umożliwia wyświetlenie wszystkiego, ale nie pozwala na usunięcie ani utworzenie konta magazynu ani zawartego zasobu. Zezwoli również na dostęp do odczytu i zapisu do wszystkich danych znajdujących się na koncie magazynu za pośrednictwem dostępu do kluczy konta magazynu. |
| [Współautor Redis Cache](#redis-cache-contributor) | Umożliwia zarządzanie pamięciami podręcznymi Redis, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor zasad zasobów (wersja zapoznawcza)](#resource-policy-contributor-preview) | Przeglądania Wypełnianie użytkowników z umowy EA z prawami do tworzenia/modyfikowania zasad zasobów, tworzenia biletów pomocy technicznej i odczytywania zasobów/hierarchii. |
| [Współautor kolekcji zadań usługi Scheduler](#scheduler-job-collections-contributor) | Umożliwia zarządzanie kolekcjami zadań harmonogramu, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor Search Service](#search-service-contributor) | Umożliwia zarządzanie usługami wyszukiwania, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Administrator zabezpieczeń](#security-admin) | Tylko w Security Center: Może wyświetlać zasady zabezpieczeń, wyświetlać Stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia, odrzucać alerty i zalecenia |
| [Security Manager (starsza wersja)](#security-manager-legacy) | Jest to Starsza rola. Zamiast tego użyj administratora zabezpieczeń |
| [Czytelnik zabezpieczeń](#security-reader) | Tylko w Security Center: Może wyświetlać zalecenia i alerty, wyświetlać zasady zabezpieczeń, wyświetlać Stany zabezpieczeń, ale nie mogą wprowadzać zmian |
| [Współautor Site Recovery](#site-recovery-contributor) | Umożliwia zarządzanie usługą Site Recovery z wyjątkiem tworzenia magazynu i przypisywania ról |
| [Operator Site Recovery](#site-recovery-operator) | Umożliwia przełączenie w tryb failover i powrót po awarii, ale nie wykonywanie innych operacji zarządzania Site Recovery |
| [Site Recovery czytelnik](#site-recovery-reader) | Umożliwia wyświetlanie stanu Site Recovery, ale nie wykonywanie innych operacji zarządzania |
| [Współautor konta kotwic przestrzennych](#spatial-anchors-account-contributor) | Umożliwia zarządzanie zakotwiczeniami przestrzennymi na Twoim koncie, ale nie ich usuwanie |
| [Właściciel konta zakotwiczeń przestrzennych](#spatial-anchors-account-owner) | Umożliwia zarządzanie zakotwiczeniami przestrzennymi na Twoim koncie, w tym ich usuwanie |
| [Czytnik konta zakotwiczeń przestrzennych](#spatial-anchors-account-reader) | Umożliwia lokalizowanie i odczytywanie właściwości kotwic przestrzennych na Twoim koncie |
| [Współautor bazy danych SQL](#sql-db-contributor) | Umożliwia zarządzanie bazami danych SQL, ale nie umożliwia uzyskiwania do nich dostępu. Nie można również zarządzać zasadami związanymi z zabezpieczeniami ani ich nadrzędnymi serwerami SQL. |
| [Współautor wystąpienia zarządzanego SQL](#sql-managed-instance-contributor) | Umożliwia zarządzanie wystąpieniami zarządzanymi SQL i wymaganą konfiguracją sieci, ale nie zapewnia dostępu innym osobom. |
| [SQL Security Manager](#sql-security-manager) | Umożliwia zarządzanie zasadami związanymi z zabezpieczeniami serwerów SQL i baz danych, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor SQL Server](#sql-server-contributor) | Umożliwia zarządzanie serwerami i bazami danych SQL, ale nie umożliwia uzyskiwania do nich dostępu ani zasad związanych z zabezpieczeniami. |
| [Współautor konta magazynu](#storage-account-contributor) | Umożliwia zarządzanie kontami magazynu. Zapewnia dostęp do klucza konta, który może służyć do uzyskiwania dostępu do danych za pośrednictwem autoryzacji klucza współużytkowanego. |
| [Rola usługi operatora kluczy konta magazynu](#storage-account-key-operator-service-role) | Zezwala na wyświetlanie i ponowne generowanie kluczy dostępu do konta magazynu. |
| [Współautor danych obiektu blob magazynu](#storage-blob-data-contributor) | Odczytuj, zapisuj i usuwaj kontenery i obiekty blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Właściciel danych obiektów blob magazynu](#storage-blob-data-owner) | Zapewnia pełen dostęp do kontenerów obiektów blob i danych usługi Azure Storage, w tym do przypisywania kontroli dostępu POSIX. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Czytnik danych obiektów blob magazynu](#storage-blob-data-reader) | Odczytuj i wyświetlaj kontenery usługi Azure Storage oraz obiekty blob. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Delegat obiektów blob magazynu](#storage-blob-delegator) | Pobierz klucz delegowania użytkownika, którego można następnie użyć do utworzenia sygnatury dostępu współdzielonego dla kontenera lub obiektu BLOB podpisanego przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tworzenie skojarzeń zabezpieczeń dla delegowania użytkowników](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
| [Współautor udziałów SMB danych plików magazynu](#storage-file-data-smb-share-contributor) | Zezwala na dostęp do odczytu, zapisu i usuwania w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB |
| [Współautor współautora udziału SMB danych plików magazynu](#storage-file-data-smb-share-elevated-contributor) | Zezwala na dostęp do odczytu, zapisu, usuwania i modyfikowania uprawnień NTFS w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB |
| [Czytnik udziałów SMB danych plików magazynu](#storage-file-data-smb-share-reader) | Zezwala na dostęp do odczytu do udziału plików platformy Azure za pośrednictwem protokołu SMB |
| [Współautor danych kolejki magazynu](#storage-queue-data-contributor) | Odczytuj, zapisuj i usuwaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Procesor komunikatów danych kolejki magazynu](#storage-queue-data-message-processor) | Wgląd, pobieranie i usuwanie wiadomości z kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Nadawca komunikatu o danych kolejki magazynu](#storage-queue-data-message-sender) | Dodawanie komunikatów do kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Czytnik danych kolejki magazynu](#storage-queue-data-reader) | Odczytuj i wyświetlaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
| [Współautor żądania pomocy technicznej](#support-request-contributor) | Pozwala tworzyć żądania pomocy technicznej i zarządzać nimi |
| [Współautor Traffic Manager](#traffic-manager-contributor) | Umożliwia zarządzanie profilami Traffic Manager, ale nie pozwala na kontrolowanie użytkowników, którzy mają do nich dostęp. |
| [Administrator dostępu użytkowników](#user-access-administrator) | Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure. |
| [Logowanie administratora maszyny wirtualnej](#virtual-machine-administrator-login) | Wyświetl Virtual Machines w portalu i zaloguj się jako administrator |
| [Współautor maszyny wirtualnej](#virtual-machine-contributor) | Umożliwia zarządzanie maszynami wirtualnymi, ale nie dostęp do nich, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone. |
| [Logowanie użytkownika maszyny wirtualnej](#virtual-machine-user-login) | Wyświetl Virtual Machines w portalu i zaloguj się jako zwykły użytkownik. |
| [Współautor planu sieci Web](#web-plan-contributor) | Umożliwia zarządzanie planami sieci Web dla witryn internetowych, ale nie umożliwia uzyskiwania do nich dostępu. |
| [Współautor witryny sieci Web](#website-contributor) | Umożliwia zarządzanie witrynami sieci Web (nie planami internetowymi), ale nie umożliwia uzyskiwania do nich dostępu. |


## <a name="owner"></a>Właściciel
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie wszystko, w tym dostęp do zasobów. |
> | **Identyfikator** | 8e3af657-a8ff-443c-a75c-2fe8c4bcb635 |
> | **Akcje** |  |
> | * | Twórz zasoby wszystkich typów i zarządzaj nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="contributor"></a>Współautor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie wszystko z wyjątkiem dostępu do zasobów. |
> | **Identyfikator** | b24988ac-6180-42a0-ab88-20f7382dd24c |
> | **Akcje** |  |
> | * | Twórz zasoby wszystkich typów i zarządzaj nimi |
> | **Nonaruszone** |  |
> | Microsoft.Authorization/*/Delete | Usuwanie ról, przypisań zasad, definicji zasad i definicji zestawów zasad |
> | Microsoft.Authorization/*/Write | Tworzenie ról, przypisań ról, przypisań zasad, definicji zasad i definicji zestawów zasad |
> | Microsoft.Authorization/elevateAccess/Action | Przyznaje administratorowi dostępu użytkownika wywołującego w zakresie dzierżawy |
> | Microsoft. plan/blueprintAssignments/zapis | Utwórz lub zaktualizuj wszystkie przypisania strategii |
> | Microsoft. plan/blueprintAssignments/usuwanie | Usuń wszystkie przypisania strategii |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="reader"></a>Czytelnik
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia wyświetlenie wszystkiego, ale nie wprowadza żadnych zmian. |
> | **Identyfikator** | acdd72a7-3385-48ef-bd42-f606fba81ae7 |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="acrdelete"></a>AcrDelete
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | usuwanie rekordu acr |
> | **Identyfikator** | c2f4ef07-c644-48eb-af81-4b1b4947fb11 |
> | **Akcje** |  |
> | Microsoft. ContainerRegistry/rejestry/artefakty/usuwanie | Usuń artefakt w rejestrze kontenerów. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="acrimagesigner"></a>AcrImageSigner
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | osoba podpisująca obraz ACR |
> | **Identyfikator** | 6cef56e8-d556-48e5-a04f-b8e64114680f |
> | **Akcje** |  |
> | Microsoft. ContainerRegistry/rejestry/rejestrowanie/zapisywanie | Wypychanie/Ściąganie metadanych zaufania zawartości dla rejestru kontenerów. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="acrpull"></a>AcrPull
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | acr pull |
> | **Identyfikator** | 7f951dda-4ed3-4680-a7ca-43fe172d538d |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Ściąganie lub pobieranie obrazów z rejestru kontenerów. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="acrpush"></a>AcrPush
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | acr push |
> | **Identyfikator** | 8311e382-0749-4cb8-b61a-304f252e45ec |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/pull/read | Ściąganie lub pobieranie obrazów z rejestru kontenerów. |
> | Microsoft.ContainerRegistry/registries/push/write | Wypchnij lub Zapisz obrazy do rejestru kontenerów. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="acrquarantinereader"></a>AcrQuarantineReader
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | czytnik danych kwarantanny ACR |
> | **Identyfikator** | cdda3590-29a3-44f6-95f2-9f980659eb04 |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Ściąganie lub pobieranie obrazów z kwarantanny z rejestru kontenerów |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="acrquarantinewriter"></a>AcrQuarantineWriter
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | moduł zapisywania danych kwarantanny ACR |
> | **Identyfikator** | c8d4ff99-41c3-41a8-9f60-21dfdad59608 |
> | **Akcje** |  |
> | Microsoft.ContainerRegistry/registries/quarantineRead/read | Ściąganie lub pobieranie obrazów z kwarantanny z rejestru kontenerów |
> | Microsoft. ContainerRegistry/rejestry/quarantineWrite/zapis | Zapisywanie/modyfikowanie stanu kwarantanny obrazów z kwarantanny |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="api-management-service-contributor"></a>Współautor usługi API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może zarządzać usługą i interfejsami API |
> | **Identyfikator** | 312a565d-c81f-4fd8-895a-4e21e48d571c |
> | **Akcje** |  |
> | Microsoft.ApiManagement/service/* | Utwórz usługę API Management i zarządzaj nią |
> | Microsoft.Authorization/*/read | Autoryzacja odczytu |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="api-management-service-operator-role"></a>Rola Operator usługi API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może zarządzać usługą, ale nie z interfejsów API |
> | **Identyfikator** | e022efe7-f5ba-4159-bbe4-b44f577e9b61 |
> | **Akcje** |  |
> | Microsoft.ApiManagement/service/*/read | Odczytaj API Management wystąpienia usługi |
> | Microsoft.ApiManagement/service/backup/action | Usługa tworzenia kopii zapasowych API Management do określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Microsoft.ApiManagement/service/delete | Usuń wystąpienie usługi API Management |
> | Microsoft.ApiManagement/service/managedeployments/action | Zmień jednostkę SKU/jednostki, Dodaj/Usuń wdrożenia regionalne usługi API Management |
> | Microsoft.ApiManagement/service/read | Odczytaj metadane dla wystąpienia usługi API Management |
> | Microsoft.ApiManagement/service/restore/action | Przywracanie usługi API Management z określonego kontenera na koncie magazynu podanego przez użytkownika |
> | Microsoft.ApiManagement/service/updatecertificate/action | Przekaż certyfikat SSL dla usługi API Management |
> | Microsoft.ApiManagement/service/updatehostname/action | Instalowanie, aktualizowanie lub usuwanie niestandardowych nazw domen dla usługi API Management |
> | Microsoft.ApiManagement/service/write | Utwórz nowe wystąpienie usługi API Management Service |
> | Microsoft.Authorization/*/read | Autoryzacja odczytu |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Pobierz klucze skojarzone z użytkownikiem |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="api-management-service-reader-role"></a>Rola Czytnik usługi API Management
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Dostęp tylko do odczytu do usługi i interfejsów API |
> | **Identyfikator** | 71522526-b88f-4d52-b57f-d31fc3546d0d |
> | **Akcje** |  |
> | Microsoft.ApiManagement/service/*/read | Odczytaj API Management wystąpienia usługi |
> | Microsoft.ApiManagement/service/read | Odczytaj metadane dla wystąpienia usługi API Management |
> | Microsoft.Authorization/*/read | Autoryzacja odczytu |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | Microsoft.ApiManagement/service/users/keys/read | Pobierz klucze skojarzone z użytkownikiem |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="application-insights-component-contributor"></a>Współautor składników usługi Application Insights
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie składnikami usługi Application Insights |
> | **Identyfikator** | ae349356-3a1b-4a5e-921d-050484c6347e |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.Insights/components/* | Tworzenie składników usługi Insights i zarządzanie nimi |
> | Microsoft.Insights/webtests/* | Tworzenie testów sieci Web i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="application-insights-snapshot-debugger"></a>Application Insights Snapshot Debugger
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Daje użytkownikowi uprawnienia do wyświetlania i pobierania migawek debugowania zebranych za pomocą Snapshot Debugger Application Insights. Należy zauważyć, że te uprawnienia nie są uwzględnione w rolach [właściciel](#owner) lub [współautor](#contributor) . |
> | **Identyfikator** | 08954f03-6346-4c2e-81c0-ec3a5cfae23b |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="automation-job-operator"></a>Operator zadań usługi Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Twórz zadania i zarządzaj nimi za pomocą elementów Runbook usługi Automation. |
> | **Identyfikator** | 4fe576fe-1146-4730-92eb-48519fa6bf9f |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Microsoft.Automation/automationAccounts/jobs/read | Pobiera zadanie Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Wznawia zadanie Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Kończy zadanie Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadania Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Wstrzymuje zadanie Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Tworzy zadanie Azure Automation |
> | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Pobiera dane wyjściowe zadania |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="automation-operator"></a>Operator automatyzacji
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Operatory automatyzacji mogą uruchamiać, zatrzymywać, wstrzymywać i wznawiać zadania |
> | **Identyfikator** | d3881f73-407a-4167-8283-e981cbba0404 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read | Odczytuje zasoby hybrydowego procesu roboczego elementu Runbook |
> | Microsoft.Automation/automationAccounts/jobs/read | Pobiera zadanie Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/resume/action | Wznawia zadanie Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/stop/action | Kończy zadanie Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/streams/read | Pobiera strumień zadania Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/suspend/action | Wstrzymuje zadanie Azure Automation |
> | Microsoft.Automation/automationAccounts/jobs/write | Tworzy zadanie Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/read | Pobiera harmonogram zadań Azure Automation |
> | Microsoft.Automation/automationAccounts/jobSchedules/write | Tworzy harmonogram zadań Azure Automation |
> | Microsoft.Automation/automationAccounts/linkedWorkspace/read | Pobiera obszar roboczy połączony z kontem usługi Automation |
> | Microsoft.Automation/automationAccounts/read | Pobiera konto Azure Automation |
> | Microsoft.Automation/automationAccounts/runbooks/read | Pobiera element Runbook Azure Automation |
> | Microsoft.Automation/automationAccounts/schedules/read | Pobiera zasób harmonogramu Azure Automation |
> | Microsoft.Automation/automationAccounts/schedules/write | Tworzy lub aktualizuje zasób harmonogramu Azure Automation |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft. Automation/automationAccounts/Jobs/Output/Read | Pobiera dane wyjściowe zadania |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="automation-runbook-operator"></a>Operator elementów runbook usługi Automation
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Odczytywanie właściwości elementu Runbook — aby można było tworzyć zadania elementu Runbook. |
> | **Identyfikator** | 5fb5aef8-1081-4b8e-bb16-9d5d0385bab5 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Automation/automationAccounts/runbooks/read | Pobiera element Runbook Azure Automation |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="avere-contributor"></a>Współautor klastra Avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Można utworzyć klaster avere vFXT i zarządzać nim. |
> | **Identyfikator** | 4f8fab4f-1852-4a58-a46a-8eaf358af14a |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Compute/*/read |  |
> | Microsoft.Compute/availabilitySets/* |  |
> | Microsoft.Compute/virtualMachines/* |  |
> | Microsoft. COMPUTE/disks/* |  |
> | Microsoft.Network/*/read |  |
> | Microsoft.Network/networkInterfaces/* |  |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/read | Pobiera definicję podsieci sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | Microsoft.Network/networkSecurityGroups/join/action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Storage/*/read |  |
> | Microsoft.Storage/storageAccounts/* |  |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/resources/read | Pobiera zasoby dla grupy zasobów. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/usuwanie | Zwraca wynik usunięcia obiektu BLOB |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/odczyt | Zwraca obiekt BLOB lub listę obiektów BLOB |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/zapis | Zwraca wynik zapisania obiektu BLOB |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="avere-operator"></a>Operator klastra Avere
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Używane przez klaster avere vFXT do zarządzania klastrem |
> | **Identyfikator** | c025889f-8102-4ebf-b32c-fc0c6f0c6bd9 |
> | **Akcje** |  |
> | Microsoft.Compute/virtualMachines/read | Pobierz właściwości maszyny wirtualnej |
> | Microsoft.Network/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft.Network/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/read | Pobiera definicję podsieci sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Microsoft.Network/networkSecurityGroups/join/action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/usuwanie | Zwraca wynik usunięcia kontenera |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/odczyt | Zwraca listę kontenerów |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Zwraca wynik umieszczenia kontenera obiektów BLOB. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/usuwanie | Zwraca wynik usunięcia obiektu BLOB |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/odczyt | Zwraca obiekt BLOB lub listę obiektów BLOB |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/zapis | Zwraca wynik zapisania obiektu BLOB |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="azure-event-hubs-data-owner"></a>Właściciel danych Event Hubs platformy Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia pełny dostęp do zasobów usługi Azure Event Hubs. |
> | **Identyfikator** | f526a384-b230-433a-b45c-95f59c4a2dec |
> | **Akcje** |  |
> | Microsoft. EventHub/* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. EventHub/* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="azure-event-hubs-data-receiver"></a>Usługa Azure Event Hubs Data Receiver
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na odbieranie dostępu do zasobów usługi Azure Event Hubs. |
> | **Identyfikator** | a638d3c7-ab3a-418d-83e6-5f17a39d4fde |
> | **Akcje** |  |
> | Microsoft.EventHub/*/eventhubs/consumergroups/read |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft.EventHub/*/receive/action |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="azure-event-hubs-data-sender"></a>Nadawca danych Event Hubs platformy Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na wysyłanie dostępu do zasobów Event Hubs platformy Azure. |
> | **Identyfikator** | 2b629674-e913-4c01-ae53-ef4638d8f975 |
> | **Akcje** |  |
> | Microsoft.EventHub/*/eventhubs/read |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. EventHub/*/Send/Action |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="azure-kubernetes-service-cluster-admin-role"></a>Rola administratora klastra usługi Kubernetes platformy Azure
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Wyświetl listę akcji poświadczeń administratora klastra. |
> | **Identyfikator** | 0ab0b1a8-8aac-4efd-b8c2-3ee1fb270be8 |
> | **Akcje** |  |
> | Microsoft.ContainerService/managedClusters/listClusterAdminCredential/action | Wyświetlanie clusterAdmin poświadczenia zarządzanego klastra |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="azure-kubernetes-service-cluster-user-role"></a>Rola użytkownika klastra usługi Azure Kubernetes Service
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Wyświetl listę akcji poświadczeń użytkownika klastra. |
> | **Identyfikator** | 4abbcc35-e782-43d8-92c5-2d3f1bd2253f |
> | **Akcje** |  |
> | Microsoft.ContainerService/managedClusters/listClusterUserCredential/action | Wyświetlanie clusterUser poświadczenia zarządzanego klastra |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="azure-maps-data-reader-preview"></a>Czytnik danych Azure Maps (wersja zapoznawcza)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Przyznaje dostęp do odczytu danych związanych z mapowaniem z konta usługi Azure Maps. |
> | **Identyfikator** | 423170ca-a8f6-4b0f-8487-9e4eb8f49bfa |
> | **Akcje** |  |
> | *dawaj* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft.Maps/accounts/data/read | Przyznaje dostęp do odczytu do konta Maps. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="azure-service-bus-data-owner"></a>Właściciel danych Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia pełny dostęp do zasobów Azure Service Bus. |
> | **Identyfikator** | 090c5cfd-751d-490a-894a-3ce6f1109419 |
> | **Akcje** |  |
> | Microsoft. ServiceBus/* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. ServiceBus/* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="azure-service-bus-data-receiver"></a>Azure Service Bus odbiorca danych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia uzyskanie dostępu do zasobów Azure Service Bus. |
> | **Identyfikator** | 4f6d3b9b-027b-4f4c-9142-0e5a2a2247e0 |
> | **Akcje** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/subscriptions/Read |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. ServiceBus/*/Receive/Action |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="azure-service-bus-data-sender"></a>Nadawca danych Azure Service Bus
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na dostęp do Azure Service Bus zasobów. |
> | **Identyfikator** | 69a216fc-b8fb-44d8-bc22-1f3c2cd27a39 |
> | **Akcje** |  |
> | Microsoft. ServiceBus/*/Queues/Read |  |
> | Microsoft. ServiceBus/*/topics/Read |  |
> | Microsoft. ServiceBus/*/topics/subscriptions/Read |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. ServiceBus/*/Send/Action |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="azure-stack-registration-owner"></a>Właściciel rejestracji w usłudze Azure Stack
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie rejestracjami Azure Stack. |
> | **Identyfikator** | 6f12a6df-dd06-4f3e-bcb1-ce8be600526a |
> | **Akcje** |  |
> | Microsoft. AzureStack/registrations/Products/*/Action |  |
> | Microsoft.AzureStack/registrations/products/read | Pobiera właściwości produktu Azure Stack Marketplace |
> | Microsoft.AzureStack/registrations/read | Pobiera właściwości rejestracji Azure Stack |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="backup-contributor"></a>Współautor kopii zapasowych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie usługą kopii zapasowych, ale nie może tworzyć magazynów i zapewniać dostępu innym osobom |
> | **Identyfikator** | 5e467623-bb1f-42f4-a55d-6e525e11384b |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | Zarządzanie wynikami operacji zarządzania kopiami zapasowymi |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Tworzenie kontenerów kopii zapasowych w ramach sieci szkieletowej magazynu Recovery Services i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Tworzenie zadań tworzenia kopii zapasowych i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportuj zadania |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | Tworzenie metadanych związanych z zarządzaniem kopiami zapasowymi i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Twórz wyniki operacji zarządzania kopiami zapasowymi i zarządzaj nimi |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/* | Tworzenie zasad tworzenia kopii zapasowych i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Utwórz elementy, których kopię zapasową można utworzyć, i zarządzaj nimi |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | Tworzenie elementów kopii zapasowych i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | Tworzenie kontenerów z elementami kopii zapasowych i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupSecurityPIN/* |  |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/* | Tworzenie i zarządzanie certyfikatami związanymi z tworzeniem kopii zapasowych w magazynie Recovery Services |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Utwórz rozszerzone informacje powiązane z magazynem i zarządzaj nimi |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Tworzenie zarejestrowanych tożsamości i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/usages/* | Tworzenie i zarządzanie użyciem magazynu Recovery Services |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft. RecoveryServices/magazyny/backupconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Operacja walidacji względem chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Pobierz wszystkie kontenery, które można objąć ochroną |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Weryfikuj funkcje |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Microsoft.RecoveryServices/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Wyświetl listę wszystkich opcji ochrony kopii zapasowej |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="backup-operator"></a>Operator kopii zapasowych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie usługami kopii zapasowych, z wyjątkiem usuwania kopii zapasowych, tworzenia magazynu i udzielania dostępu innym osobom |
> | **Identyfikator** | 00c29273-979b-4161-815c-10b084fb9324 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | Zwraca stan operacji |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | Pobiera wynik operacji wykonanej na kontenerze ochrony. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | Wykonuje kopię zapasową elementu chronionego. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | Pobiera wynik operacji wykonanej na elementach chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationsStatus/read | Zwraca stan operacji wykonanej na elementach chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/provisionInstantItemRecovery/action | Zapewnij natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | Pobierz punkty odzyskiwania dla elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | Przywróć punkty odzyskiwania dla elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/revokeInstantItemRecovery/action | Odwołaj natychmiastowe odzyskiwanie elementu dla chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Utwórz chroniony element kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | Zwraca wszystkie zarejestrowane kontenery |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/refreshContainers/action | Odświeża listę kontenerów |
> | Microsoft.RecoveryServices/Vaults/backupJobs/* | Tworzenie zadań tworzenia kopii zapasowych i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Eksportuj zadania |
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/* | Twórz wyniki operacji zarządzania kopiami zapasowymi i zarządzaj nimi |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | Utwórz elementy, których kopię zapasową można utworzyć, i zarządzaj nimi |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/write | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/write | Operacji rejestrowania kontenera usługi można użyć do zarejestrowania kontenera z usługą odzyskiwania. |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/* |  |
> | Microsoft.RecoveryServices/Vaults/backupValidateOperation/action | Operacja walidacji względem chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Pobierz stan operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/write | Tworzy zarejestrowany kontener |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/inquire/action | Wykonaj zapytanie dotyczące obciążeń w kontenerze |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Utwórz opcję ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Pobierz opcję ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectableContainers/read | Pobierz wszystkie kontenery, które można objąć ochroną |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | Microsoft.RecoveryServices/locations/backupPreValidateProtection/action |  |
> | Microsoft.RecoveryServices/locations/backupValidateFeatures/action | Weryfikuj funkcje |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Microsoft.RecoveryServices/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Wyświetl listę wszystkich opcji ochrony kopii zapasowej |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="backup-reader"></a>Czytelnik kopii zapasowych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może wyświetlać usługi kopii zapasowej, ale nie może wprowadzać zmian |
> | **Identyfikator** | a795c7a0-d4a2-40c1-ae25-d81f01202912 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
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
> | Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read |  |
> | Microsoft.RecoveryServices/Vaults/backupOperationResults/read | Zwraca wynik operacji tworzenia kopii zapasowej magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | Pobierz wyniki operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | Zwraca listę wszystkich elementów chronionych. |
> | Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | Zwraca wszystkie kontenery należące do subskrypcji |
> | Microsoft.RecoveryServices/Vaults/backupUsageSummaries/read | Zwraca podsumowania elementów chronionych i serwerów chronionych dla Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft.RecoveryServices/Vaults/backupstorageconfig/read | Zwraca konfigurację magazynu dla magazynu Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupconfig/read | Zwraca konfigurację magazynu Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupOperations/read | Zwraca stan operacji kopii zapasowej dla magazynu Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/operations/read | Pobierz stan operacji zasad. |
> | Microsoft.RecoveryServices/Vaults/backupEngines/read | Zwraca wszystkie serwery zarządzania kopiami zapasowymi zarejestrowane w magazynie. |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/read | Pobierz opcję ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/items/read | Pobierz wszystkie elementy w kontenerze |
> | Microsoft.RecoveryServices/locations/backupStatus/action | Sprawdź stan kopii zapasowej Recovery Services magazynów |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/* |  |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/write | Rozwiązuje alert. |
> | Microsoft.RecoveryServices/operations/read | Operacja zwraca listę operacji dla dostawcy zasobów |
> | Microsoft.RecoveryServices/locations/operationStatus/read | Pobiera stan operacji dla danej operacji |
> | Microsoft.RecoveryServices/Vaults/backupProtectionIntents/read | Wyświetl listę wszystkich opcji ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="billing-reader"></a>Czytelnik rozliczeń
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na dostęp do odczytu do danych rozliczeniowych |
> | **Identyfikator** | fa23ad8b-c56e-40d8-ac0c-ce449e1d2c64 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Billing/*/read | Odczytaj informacje o rozliczeniach |
> | Microsoft.Commerce/*/read |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.Management/managementGroups/read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="biztalk-contributor"></a>Współautor usługi BizTalk
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie usługami BizTalk Services, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 5e3c6656-6cfa-4708-81fe-0de47ac73342 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.BizTalkServices/BizTalk/* | Tworzenie usługi BizTalk Services i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="blockchain-member-node-access-preview"></a>Dostęp do węzła elementu członkowskiego łańcucha bloków (wersja zapoznawcza)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na dostęp do węzłów składowych łańcucha bloków |
> | **Identyfikator** | 31a002a1-acaf-453e-8a5b-297c9ca1ea24 |
> | **Akcje** |  |
> | Microsoft.Blockchain/blockchainMembers/transactionNodes/read | Pobiera lub Wyświetla istniejące węzły transakcji składowych łańcucha bloków. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. łańcucha bloków/blockchainMembers/transactionNodes/Connect/Action | Nawiązuje połączenie z węzłem transakcji elementu członkowskiego łańcucha bloków. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="blueprint-contributor"></a>Współautor planu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może zarządzać definicjami planu, ale nie należy ich przypisywać. |
> | **Identyfikator** | 41077137-e803-4205-871c-5a86e6a753b4 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft. plan/plany/* | Twórz definicje planów lub artefakty planów i zarządzaj nimi. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="blueprint-operator"></a>Operator planu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może przypisywać istniejące opublikowane plany, ale nie może tworzyć nowych planów. Uwaga: to działanie działa tylko wtedy, gdy przypisanie zostało wykonane przy użyciu tożsamości zarządzanej przypisanej przez użytkownika. |
> | **Identyfikator** | 437d2ced-4a38-4302-8479-ed2bcb43d090 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft. plan/blueprintAssignments/* | Utwórz przypisania planu i zarządzaj nimi. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cdn-endpoint-contributor"></a>Współautor punktu końcowego usługi CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może zarządzać punktami końcowymi usługi CDN, ale nie może udzielić dostępu innym użytkownikom. |
> | **Identyfikator** | 426e0c7f-0c7e-4658-b36f-ff54d6c29b45 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cdn-endpoint-reader"></a>Czytelnik punktu końcowego usługi CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może wyświetlać punkty końcowe usługi CDN, ale nie może wprowadzać zmian. |
> | **Identyfikator** | 871e35f6-b5c1-49cc-a043-bde969a0f2cd |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/endpoints/*/read |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cdn-profile-contributor"></a>Współautor profilu CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może zarządzać profilami sieci CDN i ich punktami końcowymi, ale nie może udzielać dostępu innym użytkownikom. |
> | **Identyfikator** | ec156ff8-a8d1-4d15-830c-5b80698ca432 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cdn-profile-reader"></a>Czytelnik profilu usługi CDN
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może wyświetlać profile usługi CDN i ich punkty końcowe, ale nie może wprowadzać zmian. |
> | **Identyfikator** | 8f96442b-4075-438f-813d-ad51ab4019af |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Cdn/edgenodes/read |  |
> | Microsoft.Cdn/operationresults/* |  |
> | Microsoft.Cdn/profiles/*/read |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="classic-network-contributor"></a>Współautor klasycznej sieci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie sieciami klasycznymi, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | b34d265f-36f7-4a0d-a4d4-e158ca92e90f |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Autoryzacja odczytu |
> | Microsoft.ClassicNetwork/* | Tworzenie klasycznych sieci i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="classic-storage-account-contributor"></a>Współautor klasycznego konta magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie klasycznymi kontami magazynu, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 86e8f5dc-a6e9-4c67-9d15-de283e8eac25 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Autoryzacja odczytu |
> | Microsoft.ClassicStorage/storageAccounts/* | Tworzenie kont magazynu i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="classic-storage-account-key-operator-service-role"></a>Rola usługi operatora kluczy klasycznego konta magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Operatorzy kluczy klasycznego konta magazynu mogą wyświetlać i ponownie generować klucze na klasycznych kontach magazynu |
> | **Identyfikator** | 985d6b00-f706-48f5-a6fe-d0ca12fb668d |
> | **Akcje** |  |
> | Microsoft.ClassicStorage/storageAccounts/listkeys/action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft.ClassicStorage/storageAccounts/regeneratekey/action | Generuje ponownie istniejące klucze dostępu dla konta magazynu. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="classic-virtual-machine-contributor"></a>Współautor klasycznej maszyny wirtualnej
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie klasycznymi maszynami wirtualnymi, ale nie umożliwia uzyskiwania do nich dostępu, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone. |
> | **Identyfikator** | d73bb868-a0df-4d4d-bd69-98a00b01fccb |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Autoryzacja odczytu |
> | Microsoft.ClassicCompute/domainNames/* | Tworzenie klasycznych nazw domen obliczeniowych i zarządzanie nimi |
> | Microsoft.ClassicCompute/virtualMachines/* | Tworzenie maszyn wirtualnych i zarządzanie nimi |
> | Microsoft.ClassicNetwork/networkSecurityGroups/join/action |  |
> | Microsoft.ClassicNetwork/reservedIps/link/action | Połącz zarezerwowany adres IP |
> | Microsoft.ClassicNetwork/reservedIps/read | Pobiera zastrzeżone adresy IP |
> | Microsoft.ClassicNetwork/virtualNetworks/join/action | Przyłącza do sieci wirtualnej. |
> | Microsoft.ClassicNetwork/virtualNetworks/read | Pobierz sieć wirtualną. |
> | Microsoft.ClassicStorage/storageAccounts/disks/read | Zwraca dysk konta magazynu. |
> | Microsoft.ClassicStorage/storageAccounts/images/read | Zwraca obraz konta magazynu. Przestarzałe. Użyj "Microsoft. ClassicStorage/storageAccounts/vmImages") |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft.ClassicStorage/storageAccounts/read | Zwróć konto magazynu z danym kontem. |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cognitive-services-contributor"></a>Cognitive Services — współautor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia tworzenie, odczytywanie, aktualizowanie, usuwanie i zarządzanie kluczami Cognitive Services. |
> | **Identyfikator** | 25fbc0a9-bd7c-42a3-aa1a-3b75d497ee68 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.CognitiveServices/* |  |
> | Microsoft.Features/features/read | Pobiera funkcje subskrypcji. |
> | Microsoft. Features/Providers/Features/Read | Pobiera funkcję subskrypcji dla danego dostawcy zasobów. |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | Microsoft.Insights/logDefinitions/read | Odczytaj definicje dzienników |
> | Microsoft. Insights/metricdefinitions/Read | Przeczytaj definicje metryk |
> | Microsoft.Insights/metrics/read | Odczytaj metryki |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Microsoft.Resources/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cognitive-services-data-reader-preview"></a>Czytnik danych Cognitive Services (wersja zapoznawcza)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczytywanie danych Cognitive Services. |
> | **Identyfikator** | b59867f0-fa02-499b-be73-45a86b5b3e1c |
> | **Akcje** |  |
> | *dawaj* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cognitive-services-user"></a>Cognitive Services — użytkownik
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczytywanie i wyświetlanie listy kluczy Cognitive Services. |
> | **Identyfikator** | a97b65f3-24c7-4388-baec-2e87135dc908 |
> | **Akcje** |  |
> | Microsoft.CognitiveServices/*/read |  |
> | Microsoft.CognitiveServices/accounts/listkeys/action | Klucze list |
> | Microsoft.Insights/alertRules/read | Odczytaj alert dotyczący metryki klasycznej |
> | Microsoft.Insights/diagnosticSettings/read | Odczytaj ustawienie diagnostyczne zasobu |
> | Microsoft.Insights/logDefinitions/read | Odczytaj definicje dzienników |
> | Microsoft. Insights/metricdefinitions/Read | Przeczytaj definicje metryk |
> | Microsoft.Insights/metrics/read | Odczytaj metryki |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft.Resources/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Microsoft.Resources/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft.CognitiveServices/* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cosmos-db-account-reader-role"></a>Rola czytelnika konta usługi Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może odczytywać Azure Cosmos DB dane konta. Aby zarządzać kontami Azure Cosmos DB, zobacz [współautor konta usługi DocumentDB](#documentdb-account-contributor) . |
> | **Identyfikator** | fbdf93bf-df7d-467e-a4d2-9458aa1360c8 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytaj role i przypisania ról, może odczytywać uprawnienia przypisane do każdego użytkownika |
> | Microsoft.DocumentDB/*/read | Odczytaj dowolną kolekcję |
> | Microsoft.DocumentDB/databaseAccounts/readonlykeys/action | Odczytuje klucze tylko do odczytu konta bazy danych. |
> | Microsoft.Insights/MetricDefinitions/read | Przeczytaj definicje metryk |
> | Microsoft.Insights/Metrics/read | Odczytaj metryki |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cosmos-db-operator"></a>Operator usługi Cosmos DB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie kontami Azure Cosmos DB, ale nie dostęp do danych w nich. Uniemożliwia dostęp do kluczy konta i parametrów połączenia. |
> | **Identyfikator** | 230815da-be43-4aae-9cb4-875f7bd000aa |
> | **Akcje** |  |
> | Microsoft.DocumentDb/databaseAccounts/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | Microsoft.DocumentDB/databaseAccounts/readonlyKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/regenerateKey/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listKeys/* |  |
> | Microsoft.DocumentDB/databaseAccounts/listConnectionStrings/* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cosmosbackupoperator"></a>CosmosBackupOperator
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może przesłać żądanie przywracania dla bazy danych Cosmos DB lub kontenera dla konta |
> | **Identyfikator** | db7b14f2-5adf-42da-9f96-f2ee17bab5cb |
> | **Akcje** |  |
> | Microsoft. DocumentDB/databaseAccounts/kopia zapasowa/akcja | Prześlij żądanie skonfigurowania kopii zapasowej |
> | Microsoft. DocumentDB/databaseAccounts/Restore/Action | Prześlij żądanie przywrócenia |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cost-management-contributor"></a>Cost Management — współautor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia wyświetlanie kosztów i zarządzanie konfiguracją kosztów (np. budżetów, eksportów) |
> | **Identyfikator** | 434105ed-43f6-45c7-a02f-909b2ba83430 |
> | **Akcje** |  |
> | Microsoft. zużycie/* |  |
> | Microsoft.CostManagement/* |  |
> | Microsoft. rozliczenia/billingPeriods/odczyt |  |
> | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Advisor/configurations/read | Pobierz konfiguracje |
> | Microsoft. Advisor/zalecenia/odczyt | Odczytuje zalecenia |
> | Microsoft.Management/managementGroups/read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="cost-management-reader"></a>Cost Management — czytelnik
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może wyświetlać dane i konfigurację kosztów (np. budżetów, eksportów) |
> | **Identyfikator** | 72fafb9e-0641-4937-9268-a91bfd8191a3 |
> | **Akcje** |  |
> | Microsoft.Consumption/*/read |  |
> | Microsoft.CostManagement/*/read |  |
> | Microsoft. rozliczenia/billingPeriods/odczyt |  |
> | Microsoft. resources/subscriptions/Read | Pobiera listę subskrypcji. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Advisor/configurations/read | Pobierz konfiguracje |
> | Microsoft. Advisor/zalecenia/odczyt | Odczytuje zalecenia |
> | Microsoft.Management/managementGroups/read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="data-box-contributor"></a>Data Box — współautor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie wszystko w obszarze usługi urządzenie Data Box z wyjątkiem udzielenia dostępu innym osobom. |
> | **Identyfikator** | add466c9-e687-43fc-8d98-dfcf8d720be5 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft. DATAbox/* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="data-box-reader"></a>Data Box — czytelnik
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie usługą urządzenie Data Box, z wyjątkiem tworzenia kolejności lub edytowania szczegółów kolejności i udzielania dostępu innym osobom. |
> | **Identyfikator** | 028f4ed7-e2a9-465e-a8f4-9c0ffdfdc027 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Databox/*/read |  |
> | Microsoft. DATAbox/Jobs/listsecrets/akcja |  |
> | Microsoft.Databox/jobs/listcredentials/action | Wyświetla listę nieszyfrowanych poświadczeń związanych z kolejnością. |
> | Microsoft. DATAbox/Locations/availableSkus/Action | Ta metoda zwraca listę dostępnych jednostek SKU. |
> | Microsoft.Databox/locations/validateAddress/action | Sprawdza poprawność adresu wysyłkowego i udostępnia alternatywne adresy, jeśli istnieją. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="data-factory-contributor"></a>Współautor Data Factory
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Twórz fabryki danych i zarządzaj nimi, a także zasobami podrzędnymi. |
> | **Identyfikator** | 673868aa-7521-48a0-acc6-0f60742d39f5 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.DataFactory/dataFactories/* | Twórz fabryki danych i zarządzaj nimi. |
> | Microsoft. datafabryka/fabryki/* | Twórz fabryki danych i zarządzaj nimi. |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="data-lake-analytics-developer"></a>Deweloper usługi Data Lake Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia przesyłanie własnych zadań, monitorowanie ich i zarządzanie nimi, ale nie tworzenie ani usuwanie kont Data Lake Analytics. |
> | **Identyfikator** | 47b7735b-770e-4598-a7da-8b91488b4c88 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.BigAnalytics/accounts/* |  |
> | Microsoft.DataLakeAnalytics/accounts/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | Microsoft.BigAnalytics/accounts/Delete |  |
> | Microsoft.BigAnalytics/accounts/TakeOwnership/action |  |
> | Microsoft.BigAnalytics/accounts/Write |  |
> | Microsoft.DataLakeAnalytics/accounts/Delete | Usuń konto DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/TakeOwnership/action | Udziel uprawnień do anulowania zadań przesłanych przez innych użytkowników. |
> | Microsoft.DataLakeAnalytics/accounts/Write | Utwórz lub zaktualizuj konto DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Write | Utwórz lub zaktualizuj połączone konto kontach datalakestore konta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/dataLakeStoreAccounts/Delete | Odłączanie konta kontach datalakestore z konta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Write | Utwórz lub zaktualizuj połączone konto magazynu dla konta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/storageAccounts/Delete | Odłączanie konta magazynu od konta DataLakeAnalytics. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Write | Utwórz lub zaktualizuj regułę zapory. |
> | Microsoft.DataLakeAnalytics/accounts/firewallRules/Delete | Usuń regułę zapory. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Write | Utwórz lub zaktualizuj zasady obliczeniowe. |
> | Microsoft.DataLakeAnalytics/accounts/computePolicies/Delete | Usuń zasady obliczeń. |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="data-purger"></a>Oczyszczacz danych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może czyścić dane analizy |
> | **Identyfikator** | 150f5e0c-0603-4f03-8c7f-cf70034c4e90 |
> | **Akcje** |  |
> | Microsoft.Insights/components/*/read |  |
> | Microsoft. Insights/Components/Przeczyść/akcja | Czyszczenie danych z usługi Application Insights |
> | Microsoft.OperationalInsights/workspaces/*/read |  |
> | Microsoft. OperationalInsights/obszary robocze/przeczyszczanie/akcja | Usuń określone dane z obszaru roboczego |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="devtest-labs-user"></a>Użytkownik usługi DevTest Labs
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia łączenie, uruchamianie, ponowne uruchamianie i zamykanie maszyn wirtualnych w Azure DevTest Labs. |
> | **Identyfikator** | 76283e04-6283-4c54-8f91-bcf1374a3c64 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Compute/availabilitySets/read | Pobierz właściwości zestawu dostępności |
> | Microsoft.Compute/virtualMachines/*/read | Odczytywanie właściwości maszyny wirtualnej (rozmiary maszyn wirtualnych, stan środowiska uruchomieniowego, rozszerzenia maszyn wirtualnych itp.) |
> | Microsoft.Compute/virtualMachines/deallocate/action | Wyłącza maszynę wirtualną i zwalnia zasoby obliczeniowe |
> | Microsoft.Compute/virtualMachines/read | Pobierz właściwości maszyny wirtualnej |
> | Microsoft.Compute/virtualMachines/restart/action | Uruchamia ponownie maszynę wirtualną |
> | Microsoft.Compute/virtualMachines/start/action | Uruchamia maszynę wirtualną |
> | Microsoft.DevTestLab/*/read | Odczytywanie właściwości laboratorium |
> | Microsoft.DevTestLab/labs/claimAnyVm/action | Zarezerwuj losową maszynę wirtualną z dodaną odpowiedzialnością w laboratorium. |
> | Microsoft.DevTestLab/labs/createEnvironment/action | Twórz maszyny wirtualne w laboratorium. |
> | Microsoft.DevTestLab/labs/ensureCurrentUserProfile/action | Upewnij się, że bieżący użytkownik ma prawidłowy profil w laboratorium. |
> | Microsoft.DevTestLab/labs/formulas/delete | Usuń formuły. |
> | Microsoft.DevTestLab/labs/formulas/read | Odczytaj formuły. |
> | Microsoft.DevTestLab/labs/formulas/write | Dodaj lub zmodyfikuj formuły. |
> | Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action | Szacuje zasady laboratorium. |
> | Microsoft.DevTestLab/labs/virtualMachines/claim/action | Przejmowanie na własność istniejącej maszyny wirtualnej |
> | Microsoft. wspólny/Labs/virtualmachines/listApplicableSchedules/Action | Wyświetla listę odpowiednich harmonogramów uruchamiania/zatrzymywania (jeśli istnieją). |
> | Microsoft.DevTestLab/labs/virtualMachines/getRdpFileContents/action | Pobiera ciąg, który reprezentuje zawartość pliku RDP dla maszyny wirtualnej |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Sprzęga pulę adresów zaplecza modułu równoważenia obciążenia. Brak alertów. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Przyłącza do przychodzącej reguły NAT modułu równoważenia obciążenia. Brak alertów. |
> | Microsoft.Network/networkInterfaces/*/read | Odczytywanie właściwości interfejsu sieciowego (na przykład wszystkich modułów równoważenia obciążenia, które są częścią interfejsu sieciowego) |
> | Microsoft.Network/networkInterfaces/join/action | Przyłączanie maszyny wirtualnej do interfejsu sieciowego. Brak alertów. |
> | Microsoft.Network/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft.Network/networkInterfaces/write | Tworzy interfejs sieciowy lub aktualizuje istniejący interfejs sieciowy.  |
> | Microsoft.Network/publicIPAddresses/*/read | Odczytywanie właściwości publicznego adresu IP |
> | Microsoft.Network/publicIPAddresses/join/action | Przyłącza do publicznego adresu IP. Brak alertów. |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Microsoft.Resources/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Microsoft.Resources/deployments/read | Pobiera lub wyświetla listę wdrożeń. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Storage/storageAccounts/listKeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | **Nonaruszone** |  |
> | Microsoft.Compute/virtualMachines/vmSizes/read | Wyświetla dostępne rozmiary, do których można zaktualizować maszynę wirtualną |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="dns-zone-contributor"></a>Współautor strefy DNS
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie strefami i zestawami rekordów DNS w Azure DNS, ale nie pozwala na kontrolowanie dostępu do nich. |
> | **Identyfikator** | befefa01-2a29-4197-83a8-272ff33ce314 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.Network/dnsZones/* | Tworzenie stref i rekordów DNS oraz zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="documentdb-account-contributor"></a>Współautor konta bazy danych DocumentDB
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może zarządzać kontami Azure Cosmos DB. Azure Cosmos DB jest dawniej znany jako DocumentDB. |
> | **Identyfikator** | 5bd9cd88-fe45-4216-938b-f97437e15450 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.DocumentDb/databaseAccounts/* | Tworzenie kont Azure Cosmos DB i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="eventgrid-eventsubscription-contributor"></a>Współautor subskrypcji zdarzeń usługi Event Grid
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie operacjami subskrypcji zdarzeń EventGrid. |
> | **Identyfikator** | 428e0ff0-5e57-4d9c-a221-2c70d0e0a443 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft. EventGrid/eventSubscriptions/* |  |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Wyświetl listę globalnych subskrypcji zdarzeń według typu tematu |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Utwórz listę regionalnych subskrypcji zdarzeń |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Utwórz listę regionalnych subskrypcji zdarzeń według |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="eventgrid-eventsubscription-reader"></a>Czytelnik subskrypcji zdarzeń usługi Event Grid
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczytanie subskrypcji zdarzeń EventGrid. |
> | **Identyfikator** | 2414bbcf-6497-4faf-8c65-045460748405 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.EventGrid/eventSubscriptions/read | Odczytaj eventSubscription |
> | Microsoft.EventGrid/topicTypes/eventSubscriptions/read | Wyświetl listę globalnych subskrypcji zdarzeń według typu tematu |
> | Microsoft.EventGrid/locations/eventSubscriptions/read | Utwórz listę regionalnych subskrypcji zdarzeń |
> | Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read | Utwórz listę regionalnych subskrypcji zdarzeń według |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="hdinsight-cluster-operator"></a>Operator klastra usługi HDInsight
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczytywanie i modyfikowanie konfiguracji klastrów usługi HDInsight. |
> | **Identyfikator** | 61ed4efc-fab3-44fd-b111-e24485cc132a |
> | **Akcje** |  |
> | Microsoft. HDInsight/*/Read |  |
> | Microsoft. HDInsight/Klastry/getGatewaySettings/akcja | Pobierz ustawienia bramy dla klastra usługi HDInsight |
> | Microsoft. HDInsight/Klastry/updateGatewaySettings/akcja | Aktualizowanie ustawień bramy dla klastra usługi HDInsight |
> | Microsoft. HDInsight/Klastry/konfiguracje/* |  |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Resources/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="hdinsight-domain-services-contributor"></a>HDInsight Domain Services — współautor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może odczytywać, tworzyć, modyfikować i usuwać operacje związane z usługami domenowymi, które są potrzebne pakiet Enterprise Security usługi HDInsight |
> | **Identyfikator** | 8d8d5a11-05d3-4bda-a417-a08778121c7c |
> | **Akcje** |  |
> | Microsoft.AAD/*/read |  |
> | Microsoft. AAD/domainServices/*/Read |  |
> | Microsoft. AAD/domainServices/oucontainer/* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="intelligent-systems-account-contributor"></a>Współautor konta usługi Systemy inteligentne
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie kontami inteligentnych systemów, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 03a6d094-3444-4b3d-88af-7477090a9e5e |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.IntelligentSystems/accounts/* | Tworzenie kont inteligentnych systemów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="key-vault-contributor"></a>Współautor magazynu kluczy
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia Zarządzanie magazynami kluczy, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | f25e0fa2-a7c8-4377-a976-54943a77a395 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.KeyVault/* |  |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | Microsoft.KeyVault/locations/deletedVaults/purge/action | Przeczyść usunięty nietrwale Magazyn kluczy |
> | Microsoft.KeyVault/hsmPools/* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="lab-creator"></a>Twórca laboratorium
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia tworzenie i usuwanie zarządzanych laboratoriów z kont laboratorium platformy Azure oraz zarządzanie nimi. |
> | **Identyfikator** | b97fb8bc-a8b2-4522-a38b-dd33c7e65ead |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.LabServices/labAccounts/*/read |  |
> | Microsoft.LabServices/labAccounts/createLab/action | Utwórz laboratorium na koncie laboratorium. |
> | Microsoft. LabServices/labAccounts/Sizes/getRegionalAvailability/Action |  |
> | Microsoft. LabServices/labAccounts/getRegionalAvailability/akcja | Uzyskaj informacje o dostępności regionalnej dla każdej kategorii rozmiaru skonfigurowanej w ramach konta laboratorium |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="log-analytics-contributor"></a>Współautor usługi Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Współautor Log Analytics może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Edytowanie ustawień monitorowania obejmuje dodanie rozszerzenia maszyny wirtualnej do maszyn wirtualnych; Odczytywanie kluczy konta magazynu w celu skonfigurowania kolekcji dzienników z usługi Azure Storage; Tworzenie i Konfigurowanie kont usługi Automation; Dodawanie rozwiązań; i Konfigurowanie diagnostyki platformy Azure dla wszystkich zasobów platformy Azure. |
> | **Identyfikator** | 92aaf0da-9dab-42b6-94a3-d43ce8d16293 |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft.Automation/automationAccounts/* |  |
> | Microsoft.ClassicCompute/virtualMachines/extensions/* |  |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft.Compute/virtualMachines/extensions/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | Microsoft.OperationalInsights/* |  |
> | Microsoft.OperationsManagement/* |  |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourcegroups/deployments/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="log-analytics-reader"></a>Czytelnik usługi Log Analytics
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Log Analytics Reader może wyświetlać i przeszukiwać wszystkie dane monitorowania, a także wyświetlać ustawienia monitorowania, w tym Wyświetlanie konfiguracji diagnostyki platformy Azure na wszystkich zasobach platformy Azure. |
> | **Identyfikator** | 73c42c96-874c-492b-b04d-ab87d138a893 |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft.OperationalInsights/workspaces/analytics/query/action | Wyszukaj przy użyciu nowego aparatu. |
> | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/read | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="logic-app-contributor"></a>Współautor aplikacji logiki
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie aplikacjami logiki, ale nie umożliwia zmiany dostępu do nich. |
> | **Identyfikator** | 87a39d53-fc1b-424a-814c-f7e04687dc9e |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.ClassicStorage/storageAccounts/listKeys/action | Wyświetla listę kluczy dostępu dla kont magazynu. |
> | Microsoft.ClassicStorage/storageAccounts/read | Zwróć konto magazynu z danym kontem. |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Insights/diagnosticSettings/* | Tworzy, aktualizuje lub odczytuje ustawienia diagnostyczne dla Analysis Server |
> | Microsoft.Insights/logdefinitions/* | To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Wyświetl listę kategorii dziennika w dzienniku aktywności. |
> | Microsoft.Insights/metricDefinitions/* | Odczytaj definicje metryk (lista dostępnych typów metryk dla zasobu). |
> | Microsoft.Logic/* | Zarządza zasobami Logic Apps. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Storage/storageAccounts/listkeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Web/connectionGateways/* | Utwórz bramę połączenia i zarządzaj nią. |
> | Microsoft.Web/connections/* | Utwórz połączenie i Zarządzaj nim. |
> | Microsoft.Web/customApis/* | Tworzy niestandardowy interfejs API i zarządza nim. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Pobierz właściwości planu App Service |
> | Microsoft.Web/sites/functions/listSecrets/action | Wyświetl wpisy tajne Web Apps funkcji. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="logic-app-operator"></a>Operator aplikacji logiki
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczytywanie, Włączanie i wyłączanie aplikacji logiki, ale nie ich edytowanie ani aktualizowanie. |
> | **Identyfikator** | 515c2055-d9d4-4321-b1b9-bd0c9a0f79fe |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/*/read | Czytaj reguły alertów szczegółowych informacji |
> | Microsoft.Insights/diagnosticSettings/*/read | Pobiera ustawienia diagnostyczne dla Logic Apps |
> | Microsoft.Insights/metricDefinitions/*/read | Pobiera dostępne metryki dla Logic Apps. |
> | Microsoft.Logic/*/read | Odczytuje Logic Apps zasoby. |
> | Microsoft. Logic/Workflows/Disable/Action | Wyłącza przepływ pracy. |
> | Microsoft.Logic/workflows/enable/action | Włącza przepływ pracy. |
> | Microsoft.Logic/workflows/validate/action | Sprawdza poprawność przepływu pracy. |
> | Microsoft.Resources/deployments/operations/read | Pobiera lub wyświetla listę operacji wdrażania. |
> | Microsoft.Resources/subscriptions/operationresults/read | Pobierz wyniki operacji subskrypcji. |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Web/connectionGateways/*/read | Odczytaj bramy połączeń. |
> | Microsoft.Web/connections/*/read | Odczyt połączeń. |
> | Microsoft.Web/customApis/*/read | Odczytaj niestandardowy interfejs API. |
> | Microsoft.Web/serverFarms/read | Pobierz właściwości planu App Service |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="managed-application-operator-role"></a>Aplikacja zarządzana — rola operatora
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczytywanie i wykonywanie akcji dotyczących zasobów aplikacji zarządzanej |
> | **Identyfikator** | c7393b34-138c-406f-901b-d8cf2b17e6ae |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft.Solutions/applications/read | Pobiera listę aplikacji. |
> | Microsoft. Solutions/*/Action |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="managed-applications-reader"></a>Aplikacje zarządzane — czytelnik
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia odczytanie zasobów w zarządzanej aplikacji i zażądanie dostępu JIT. |
> | **Identyfikator** | b9331d33-8a36-4f8c-b097-4f54124fdb44 |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Solutions/jitRequests/* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="managed-identity-contributor"></a>Współautor tożsamości zarządzanych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Tworzenie, odczytywanie, aktualizowanie i usuwanie tożsamości przypisanej przez użytkownika |
> | **Identyfikator** | e40ec5ca-96e0-45a2-b4ff-59039f2c2b59 |
> | **Akcje** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/read | Pobiera istniejącą tożsamość przypisanego użytkownika |
> | Microsoft.ManagedIdentity/userAssignedIdentities/write | Tworzy nową tożsamość przypisanego użytkownika lub aktualizuje Tagi skojarzone z istniejącą tożsamością przypisanego użytkownika |
> | Microsoft.ManagedIdentity/userAssignedIdentities/delete | Usuwa istniejącą tożsamość przypisanego użytkownika |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="managed-identity-operator"></a>Operator tożsamości zarządzanych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Odczytaj i przypisz tożsamość przypisaną przez użytkownika |
> | **Identyfikator** | f1a07417-d97a-45cb-824c-7a7467783830 |
> | **Akcje** |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/read |  |
> | Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="management-group-contributor"></a>Współautor grupy zarządzania
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Rola współautora grupy zarządzania |
> | **Identyfikator** | 5d58bcaf-24a5-4b20-bdb6-eed9f69fbe4c |
> | **Akcje** |  |
> | Microsoft. Management/managementGroups/Delete | Usuń grupę zarządzania. |
> | Microsoft.Management/managementGroups/read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | Microsoft. Management/managementGroups/subskrypcje/usuwanie | Anuluj skojarzenie subskrypcji z grupą zarządzania. |
> | Microsoft. Management/managementGroups/subskrypcje/zapis | Kojarzy istniejącą subskrypcję z grupą zarządzania. |
> | Microsoft. Management/managementGroups/Write | Utwórz lub Zaktualizuj grupę zarządzania. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="management-group-reader"></a>Czytelnik grupy zarządzania
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Rola czytelnika grupy zarządzania |
> | **Identyfikator** | ac63b705-f282-497d-ac71-919bf39d939d |
> | **Akcje** |  |
> | Microsoft.Management/managementGroups/read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="monitoring-contributor"></a>Współautor monitorowania
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może odczytywać wszystkie dane monitorowania i edytować ustawienia monitorowania. Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Identyfikator** | 749f88d5-cbae-40b8-bcfc-e573ddc772fa |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft.AlertsManagement/alerts/* |  |
> | Microsoft.AlertsManagement/alertsSummary/* |  |
> | Microsoft. Insights/actiongroups/* |  |
> | Microsoft. Insights/activityLogAlerts/* |  |
> | Microsoft.Insights/AlertRules/* | Odczyt/zapis/usuwanie reguł alertów. |
> | Microsoft.Insights/components/* | Odczytaj/Zapisz/Usuń składniki Application Insights. |
> | Microsoft.Insights/DiagnosticSettings/* | Odczyt/zapis/usuwanie ustawień diagnostycznych. |
> | Microsoft.Insights/eventtypes/* | Wyświetl listę zdarzeń dziennika aktywności (zdarzenia zarządzania) w subskrypcji. To uprawnienie dotyczy zarówno dostępu programowego, jak i portalu do dziennika aktywności. |
> | Microsoft.Insights/LogDefinitions/* | To uprawnienie jest niezbędne dla użytkowników, którzy potrzebują dostępu do dzienników aktywności za pośrednictwem portalu. Wyświetl listę kategorii dziennika w dzienniku aktywności. |
> | Microsoft.Insights/metricalerts/* |  |
> | Microsoft.Insights/MetricDefinitions/* | Odczytaj definicje metryk (lista dostępnych typów metryk dla zasobu). |
> | Microsoft.Insights/Metrics/* | Odczytaj metryki dla zasobu. |
> | Microsoft.Insights/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Microsoft.Insights/scheduledqueryrules/* |  |
> | Microsoft.Insights/webtests/* | Odczytaj/Zapisz/Usuń Application Insights testy sieci Web. |
> | Microsoft. Insights/skoroszyty/* |  |
> | Microsoft.OperationalInsights/workspaces/intelligencepacks/* | Odczytaj/Zapisz/Usuń pakiety rozwiązań usługi log Analytics. |
> | Microsoft.OperationalInsights/workspaces/savedSearches/* | Odczyt/zapis/usuwanie zapisanych wyszukiwań usługi log Analytics. |
> | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | Microsoft.OperationalInsights/workspaces/sharedKeys/action | Pobiera klucze wspólne dla obszaru roboczego. Klucze te służą do łączenia agentów programu Microsoft Operational Insights z obszarem roboczym. |
> | Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* | Odczyt/zapis/Usuwanie konfiguracji usługi Magazyn analizy dzienników. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.WorkloadMonitor/monitors/* |  |
> | Microsoft.WorkloadMonitor/notificationSettings/* |  |
> | Microsoft. AlertsManagement/smartDetectorAlertRules/* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="monitoring-metrics-publisher"></a>Wydawca metryk monitorowania
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Włącza publikowanie metryk dla zasobów platformy Azure |
> | **Identyfikator** | 3913510d-42f4-4e42-8a64-420c390055eb |
> | **Akcje** |  |
> | Microsoft.Insights/Register/Action | Zarejestruj dostawcę usługi Microsoft Insights |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Insights/Metrics/Write | Metryki zapisu |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="monitoring-reader"></a>Czytelnik monitorowania
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Może odczytywać wszystkie dane monitorowania (metryki, dzienniki itp.). Zobacz też Rozpoczynanie [pracy z rolami, uprawnieniami i zabezpieczeniami przy użyciu Azure monitor](../azure-monitor/platform/roles-permissions-security.md#built-in-monitoring-roles). |
> | **Identyfikator** | 43d0d8ad-25c7-4714-9337-8ba259a9fe05 |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft.OperationalInsights/workspaces/search/action | Wykonuje zapytanie wyszukiwania |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="network-contributor"></a>Współautor sieci
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie sieciami, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 4d97b98b-1d4f-4787-a291-c67834d212e7 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.Network/* | Tworzenie sieci i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="new-relic-apm-account-contributor"></a>Współautor konta usługi New Relic APM
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie kontami i aplikacjami New Relic Application Performance Management, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 5d28c62d-5b37-4476-8438-e587778df237 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | NewRelic.APM/accounts/* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="reader-and-data-access"></a>Czytnik i dostęp do danych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia wyświetlenie wszystkiego, ale nie pozwala na usunięcie ani utworzenie konta magazynu ani zawartego zasobu. Zezwoli również na dostęp do odczytu i zapisu do wszystkich danych znajdujących się na koncie magazynu za pośrednictwem dostępu do kluczy konta magazynu. |
> | **Identyfikator** | c12c1c16-33a1-487b-954d-41c89c60f349 |
> | **Akcje** |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Microsoft. Storage/storageAccounts/ListAccountSas/akcja | Zwraca token sygnatury dostępu współdzielonego konta dla określonego konta magazynu. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="redis-cache-contributor"></a>Współautor pamięci podręcznej Redis
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie pamięciami podręcznymi Redis, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | e0f68234-74aa-48ed-b826-c38b57376e17 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Cache/redis/* | Tworzenie pamięci podręcznych Redis i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="resource-policy-contributor-preview"></a>Współautor zasad zasobów (wersja zapoznawcza)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Przeglądania Wypełnianie użytkowników z umowy EA z prawami do tworzenia/modyfikowania zasad zasobów, tworzenia biletów pomocy technicznej i odczytywania zasobów/hierarchii. |
> | **Identyfikator** | 36243c78-bf99-498c-9df9-86d9f8d28608 |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft. Authorization/policyassignments/* | Tworzenie przypisań zasad i zarządzanie nimi |
> | Microsoft. Authorization/PolicyDefinitions/* | Tworzenie definicji zasad i zarządzanie nimi |
> | Microsoft. Authorization/policysetdefinitions/* | Tworzenie zestawów zasad i zarządzanie nimi |
> | Microsoft. PolicyInsights/* |  |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="scheduler-job-collections-contributor"></a>Współautor kolekcji zadań usługi Harmonogram
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie kolekcjami zadań harmonogramu, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 188a0f2f-5c9e-469b-ae67-2aa5ce574b94 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Scheduler/jobcollections/* | Tworzenie kolekcji zadań i zarządzanie nimi |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="search-service-contributor"></a>Współautor usługi wyszukiwania
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie usługami wyszukiwania, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 7ca78c08-252a-4471-8644-bb5ff32d4ba0 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Search/searchServices/* | Tworzenie usług wyszukiwania i zarządzanie nimi |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="security-admin"></a>Administrator zabezpieczeń
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Tylko w Security Center: Może wyświetlać zasady zabezpieczeń, wyświetlać Stany zabezpieczeń, edytować zasady zabezpieczeń, wyświetlać alerty i zalecenia, odrzucać alerty i zalecenia |
> | **Identyfikator** | fb1c8493-542b-48eb-b624-b4c8fea62acd |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Authorization/policyAssignments/* | Tworzenie przypisań zasad i zarządzanie nimi |
> | Microsoft.Authorization/policyDefinitions/* | Tworzenie definicji zasad i zarządzanie nimi |
> | Microsoft.Authorization/policySetDefinitions/* | Tworzenie zestawów zasad i zarządzanie nimi |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.Management/managementGroups/read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | Microsoft.operationalInsights/workspaces/*/read | Wyświetlanie danych usługi log Analytics |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Security/* |  |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="security-manager-legacy"></a>Security Manager (starsza wersja)
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Jest to Starsza rola. Zamiast tego użyj administratora zabezpieczeń |
> | **Identyfikator** | e3d13bf0-dd5a-482e-ba6b-9b8433878d10 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.ClassicCompute/*/read | Odczytaj informacje o konfiguracji klasyczne maszyny wirtualne |
> | Microsoft.ClassicCompute/virtualMachines/*/write | Zapisz konfigurację dla klasycznych maszyn wirtualnych |
> | Microsoft.ClassicNetwork/*/read | Odczytaj informacje o konfiguracji dotyczące sieci klasycznej |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Security/* | Tworzenie i zarządzanie składnikami i zasadami zabezpieczeń |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="security-reader"></a>Czytelnik zabezpieczeń
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Tylko w Security Center: Może wyświetlać zalecenia i alerty, wyświetlać zasady zabezpieczeń, wyświetlać Stany zabezpieczeń, ale nie mogą wprowadzać zmian |
> | **Identyfikator** | 39bc4728-0917-49c7-9d2c-d95423bc2eb4 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.operationalInsights/workspaces/*/read | Wyświetlanie danych usługi log Analytics |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Security/*/read | Odczytaj składniki i zasady zabezpieczeń |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Management/managementGroups/read | Wyświetl listę grup zarządzania dla uwierzytelnionego użytkownika. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="site-recovery-contributor"></a>Współautor usługi Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie usługą Site Recovery z wyjątkiem tworzenia magazynu i przypisywania ról |
> | **Identyfikator** | 6670b86e-a3f7-4917-ac9b-5d6ab1be4567 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft.RecoveryServices/Vaults/certificates/write | Operacja Aktualizuj certyfikat zasobu aktualizuje certyfikat poświadczeń zasobu/magazynu. |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/* | Utwórz rozszerzone informacje powiązane z magazynem i zarządzaj nimi |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/* | Tworzenie zarejestrowanych tożsamości i zarządzanie nimi |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | Utwórz lub zaktualizuj ustawienia alertu replikacji |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Odczytaj wszystkie zdarzenia |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/* | Tworzenie sieci szkieletowej replikacji i zarządzanie nimi |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Tworzenie zadań replikacji i zarządzanie nimi |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/* | Tworzenie zasad replikacji i zarządzanie nimi |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | Tworzenie planów odzyskiwania i zarządzanie nimi |
> | Microsoft.RecoveryServices/Vaults/storageConfig/* | Tworzenie i zarządzanie konfiguracją magazynu Recovery Services magazynu |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Odczytaj alerty dla magazynu usługi Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="site-recovery-operator"></a>Operator usługi Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia przełączenie w tryb failover i powrót po awarii, ale nie wykonywanie innych operacji zarządzania Site Recovery |
> | **Identyfikator** | 494ae006-db33-4328-bf46-533a6560a3ca |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
> | Microsoft.RecoveryServices/locations/allocateStamp/action | AllocateStamp jest operacją wewnętrzną używaną przez usługę |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Odczytaj wszystkie ustawienia alertów |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Odczytaj wszystkie zdarzenia |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | Sprawdza spójność sieci szkieletowej |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Odczytaj wszystkie sieci szkieletowe |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/reassociateGateway/action | Skojarz ponownie bramę |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | Odnów certyfikat sieci szkieletowej |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Odczytaj wszystkie sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytaj wszystkie mapowania sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Odczytaj wszystkie kontenery ochrony |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Odczytaj dowolne elementy podlegające ochronie |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/applyRecoveryPoint/action | Zastosuj punkt odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/failoverCommit/action | Zatwierdzenie pracy w trybie failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/plannedFailover/action | Planowane przełączenie w tryb failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Odczytaj wszystkie chronione elementy |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytaj wszystkie punkty odzyskiwania replikacji |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/repairReplication/action | Napraw replikację |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/reProtect/action | Włącz ponownie ochronę chronionego elementu |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailover/action | Testowe przełączenie w tryb failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/testFailoverCleanup/action | Oczyszczanie testu przełączania do trybu failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/unplannedFailover/action | Tryb failover |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/updateMobilityService/action | Aktualizuj usługę mobilności |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Odczytaj wszystkich dostawców Recovery Services |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/refreshProvider/action | Odśwież dostawcę |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytaj wszystkie klasyfikacje magazynu |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Odczytaj dowolne vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/* | Tworzenie zadań replikacji i zarządzanie nimi |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Odczytaj wszystkie zasady |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/failoverCommit/action | Plan odzyskiwania zatwierdzania trybu failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/plannedFailover/action | Plan odzyskiwania planowego przełączania w tryb failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Zapoznaj się z planami odzyskiwania |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | Plan odzyskiwania ponownego obejmowania ochroną |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | Plan odzyskiwania testowego przełączania w tryb failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailoverCleanup/action | Plan odzyskiwania dla testowego przełączania w tryb failover |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/unplannedFailover/action | Plan odzyskiwania dla przełączania w tryb failover |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/* | Odczytaj alerty dla magazynu usługi Recovery Services |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="site-recovery-reader"></a>Czytelnik usługi Site Recovery
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia wyświetlanie stanu Site Recovery, ale nie wykonywanie innych operacji zarządzania |
> | **Identyfikator** | dbaa88c4-0c30-4179-9fb3-46319faa6149 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.RecoveryServices/locations/allocatedStamp/read | Operacja GetAllocatedStamp to operacja wewnętrzna używana przez usługę |
> | Microsoft.RecoveryServices/Vaults/extendedInformation/read | Operacja Pobierz rozszerzone informacje pobiera rozszerzone informacje o obiekcie reprezentujące zasób platformy Azure typu „magazyn” |
> | Microsoft.RecoveryServices/Vaults/monitoringAlerts/read | Pobiera alerty dla magazynu usługi Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/monitoringConfigurations/notificationConfiguration/read |  |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft.RecoveryServices/Vaults/refreshContainers/read |  |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | Operacja Pobierz wyniki operacji umożliwia pobieranie stanu operacji i wyniku operacji przesłanej asynchronicznie |
> | Microsoft.RecoveryServices/Vaults/registeredIdentities/read | Operacja Pobierz kontenery umożliwia pobieranie kontenerów zarejestrowanych dla zasobu. |
> | Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | Odczytaj wszystkie ustawienia alertów |
> | Microsoft.RecoveryServices/vaults/replicationEvents/read | Odczytaj wszystkie zdarzenia |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/read | Odczytaj wszystkie sieci szkieletowe |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | Odczytaj wszystkie sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/replicationNetworkMappings/read | Odczytaj wszystkie mapowania sieci |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/read | Odczytaj wszystkie kontenery ochrony |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectableItems/read | Odczytaj dowolne elementy podlegające ochronie |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/read | Odczytaj wszystkie chronione elementy |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | Odczytaj wszystkie punkty odzyskiwania replikacji |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationProtectionContainerMappings/read | Odczytaj wszelkie mapowania kontenerów ochrony |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationRecoveryServicesProviders/read | Odczytaj wszystkich dostawców Recovery Services |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/read | Odczytaj wszystkie klasyfikacje magazynu |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationStorageClassifications/replicationStorageClassificationMappings/read | Odczytaj dowolne mapowania klasyfikacji magazynu |
> | Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | Odczytaj dowolne vCenter |
> | Microsoft.RecoveryServices/vaults/replicationJobs/read | Odczytaj wszystkie zadania |
> | Microsoft.RecoveryServices/vaults/replicationPolicies/read | Odczytaj wszystkie zasady |
> | Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | Zapoznaj się z planami odzyskiwania |
> | Microsoft.RecoveryServices/Vaults/storageConfig/read |  |
> | Microsoft.RecoveryServices/Vaults/tokenInfo/read |  |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/vaultTokens/read | Operacja token magazynu umożliwia pobieranie tokenu magazynu dla operacji zaplecza na poziomie magazynu. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="spatial-anchors-account-contributor"></a>Współautor kont zakotwiczeń przestrzennych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie zakotwiczeniami przestrzennymi na Twoim koncie, ale nie ich usuwanie |
> | **Identyfikator** | 8bbe83f1-e2a6-4df7-8cb4-4e04d4e5c827 |
> | **Akcje** |  |
> | *dawaj* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Utwórz kotwice przestrzenne |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/odnajdywanie/odczyt | Odkryj kotwice w pobliżu |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/właściwości/odczyt | Pobierz właściwości kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Lokalizowanie kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi zakotwiczeń przestrzennych platformy Azure |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Aktualizuj właściwości kotwic przestrzennych |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="spatial-anchors-account-owner"></a>Właściciel kont zakotwiczeń przestrzennych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie zakotwiczeniami przestrzennymi na Twoim koncie, w tym ich usuwanie |
> | **Identyfikator** | 70bbe301-9835-447d-afdd-19eb3167307c |
> | **Akcje** |  |
> | *dawaj* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Create/Action | Utwórz kotwice przestrzenne |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Delete | Usuń kotwice przestrzenne |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/odnajdywanie/odczyt | Odkryj kotwice w pobliżu |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/właściwości/odczyt | Pobierz właściwości kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Lokalizowanie kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi zakotwiczeń przestrzennych platformy Azure |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Write | Aktualizuj właściwości kotwic przestrzennych |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="spatial-anchors-account-reader"></a>Czytelnik kont zakotwiczeń przestrzennych
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia lokalizowanie i odczytywanie właściwości kotwic przestrzennych na Twoim koncie |
> | **Identyfikator** | 5d51204f-eb77-4b1c-b86a-2ec626c49413 |
> | **Akcje** |  |
> | *dawaj* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/odnajdywanie/odczyt | Odkryj kotwice w pobliżu |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/właściwości/odczyt | Pobierz właściwości kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/Query/Read | Lokalizowanie kotwic przestrzennych |
> | Microsoft. MixedReality/SpatialAnchorsAccounts/submitdiag/Read | Przesyłanie danych diagnostycznych w celu poprawy jakości usługi zakotwiczeń przestrzennych platformy Azure |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="sql-db-contributor"></a>Współautor bazy danych SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie bazami danych SQL, ale nie umożliwia uzyskiwania do nich dostępu. Nie można również zarządzać zasadami związanymi z zabezpieczeniami ani ich nadrzędnymi serwerami SQL. |
> | **Identyfikator** | 9b7fa17d-e63e-47b0-bb0a-15c516ac86ec |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/databases/* | Tworzenie baz danych SQL i zarządzanie nimi |
> | Microsoft.Sql/servers/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Insights/metrics/read | Odczytaj metryki |
> | Microsoft. Insights/metricDefinitions/Read | Przeczytaj definicje metryk |
> | **Nonaruszone** |  |
> | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/Databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Edytowanie zasad inspekcji |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Edytuj ustawienia inspekcji |
> | Microsoft.Sql/servers/databases/auditRecords/read | Pobieranie rekordów inspekcji obiektów BLOB bazy danych |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Edytowanie zasad połączenia |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Edytowanie zasad maskowania danych |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Edytuj zasady alertów zabezpieczeń |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Edytuj metryki zabezpieczeń |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="sql-managed-instance-contributor"></a>Wystąpienie zarządzane SQL — współautor
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie wystąpieniami zarządzanymi SQL i wymaganą konfiguracją sieci, ale nie zapewnia dostępu innym osobom. |
> | **Identyfikator** | 4939a1f6-9ae0-4e48-a1e0-f2cbe897382d |
> | **Akcje** |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Network/networkSecurityGroups/* |  |
> | Microsoft.Network/routeTables/* |  |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/managedInstances/* |  |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Network/virtualNetworks/subnets/* |  |
> | Microsoft. Network/virtualNetworks/* |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Insights/metrics/read | Odczytaj metryki |
> | Microsoft. Insights/metricDefinitions/Read | Przeczytaj definicje metryk |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="sql-security-manager"></a>Menedżer zabezpieczeń SQL
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie zasadami związanymi z zabezpieczeniami serwerów SQL i baz danych, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 056cd41c-7e88-42e1-933e-88ba6a50c9c3 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytaj autoryzację firmy Microsoft |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/Databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/Databases/transparentDataEncryption/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Tworzenie zasad inspekcji programu SQL Server i zarządzanie nimi |
> | Microsoft.Sql/servers/auditingSettings/* | Tworzenie ustawień inspekcji programu SQL Server i zarządzanie nimi |
> | Microsoft.Sql/servers/extendedAuditingSettings/read | Pobierz szczegóły zasad inspekcji rozszerzonego serwera obiektów BLOB skonfigurowanych na danym serwerze |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Tworzenie zasad inspekcji bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Tworzenie ustawień inspekcji bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft.Sql/servers/databases/auditRecords/read | Odczytaj rekordy inspekcji |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Tworzenie zasad połączenia bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Tworzenie zasad maskowania danych bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/read | Pobierz szczegóły zasad rozszerzonej inspekcji obiektów BLOB skonfigurowanych dla danej bazy danych |
> | Microsoft.Sql/servers/databases/read | Zwróć listę baz danych lub pobiera właściwości dla określonej bazy danych. |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/read | Pobierz schemat bazy danych. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/read | Pobierz kolumnę bazy danych. |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/read | Pobierz tabelę bazy danych. |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Tworzenie zasad alertów zabezpieczeń bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Tworzenie metryk zabezpieczeń bazy danych programu SQL Server i zarządzanie nimi |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft. SQL/serwery/bazy danych/transparentDataEncryption/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/firewallRules/* |  |
> | Microsoft.Sql/servers/read | Zwróć listę serwerów lub pobiera właściwości dla określonego serwera. |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Tworzenie zasad alertów zabezpieczeń programu SQL Server i zarządzanie nimi |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="sql-server-contributor"></a>Współautor serwera SQL Server
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie serwerami i bazami danych SQL, ale nie umożliwia uzyskiwania do nich dostępu ani zasad związanych z zabezpieczeniami. |
> | **Identyfikator** | 6d8ee4ec-f05a-4a1d-8b00-a9b17e38b437 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Sql/locations/*/read |  |
> | Microsoft.Sql/servers/* | Tworzenie serwerów SQL i zarządzanie nimi |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Insights/metrics/read | Odczytaj metryki |
> | Microsoft. Insights/metricDefinitions/Read | Przeczytaj definicje metryk |
> | **Nonaruszone** |  |
> | Microsoft. SQL/managedInstances/Databases/currentSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/Databases/recommendedSensitivityLabels/* |  |
> | Microsoft. SQL/managedInstances/bazy danych/schematy/tabele/kolumny/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/securityAlertPolicies/* |  |
> | Microsoft. SQL/managedInstances/Databases/sensitivityLabels/* |  |
> | Microsoft.Sql/managedInstances/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/managedInstances/securityAlertPolicies/* |  |
> | Microsoft.Sql/managedInstances/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/auditingPolicies/* | Edytowanie zasad inspekcji programu SQL Server |
> | Microsoft.Sql/servers/auditingSettings/* | Edytowanie ustawień inspekcji programu SQL Server |
> | Microsoft.Sql/servers/databases/auditingPolicies/* | Edytowanie zasad inspekcji bazy danych programu SQL Server |
> | Microsoft.Sql/servers/databases/auditingSettings/* | Edytowanie ustawień inspekcji bazy danych programu SQL Server |
> | Microsoft.Sql/servers/databases/auditRecords/read | Odczytaj rekordy inspekcji |
> | Microsoft.Sql/servers/databases/connectionPolicies/* | Edytowanie zasad połączenia bazy danych programu SQL Server |
> | Microsoft.Sql/servers/databases/currentSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/dataMaskingPolicies/* | Edytowanie zasad maskowania danych w bazie danych programu SQL Server |
> | Microsoft.Sql/servers/databases/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/databases/recommendedSensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/schemas/tables/columns/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/securityAlertPolicies/* | Edytowanie zasad alertów zabezpieczeń bazy danych programu SQL Server |
> | Microsoft.Sql/servers/databases/securityMetrics/* | Edytuj metryki zabezpieczeń bazy danych programu SQL Server |
> | Microsoft.Sql/servers/databases/sensitivityLabels/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessments/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentScans/* |  |
> | Microsoft.Sql/servers/databases/vulnerabilityAssessmentSettings/* |  |
> | Microsoft.Sql/servers/extendedAuditingSettings/* |  |
> | Microsoft.Sql/servers/securityAlertPolicies/* | Edytuj zasady alertów zabezpieczeń programu SQL Server |
> | Microsoft.Sql/servers/vulnerabilityAssessments/* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-account-contributor"></a>Współautor konta magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie kontami magazynu. Zapewnia dostęp do klucza konta, który może służyć do uzyskiwania dostępu do danych za pośrednictwem autoryzacji klucza współużytkowanego. |
> | **Identyfikator** | 17d1049b-9a84-46fb-8f53-869881c3d3ab |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj wszystkie autoryzacje |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Insights/diagnosticSettings/* | Zarządzanie ustawieniami diagnostycznymi |
> | Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action | Dołącza do podsieci zasoby, takie jak konto magazynu lub baza danych SQL. Brak alertów. |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Storage/storageAccounts/* | Tworzenie kont magazynu i zarządzanie nimi |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-account-key-operator-service-role"></a>Rola usługi operatora kluczy konta magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na wyświetlanie i ponowne generowanie kluczy dostępu do konta magazynu. |
> | **Identyfikator** | 81a9662b-bebf-436f-a333-f67b29880f12 |
> | **Akcje** |  |
> | Microsoft.Storage/storageAccounts/listkeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Microsoft.Storage/storageAccounts/regeneratekey/action | Generuje ponownie klucze dostępu dla określonego konta magazynu. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-blob-data-contributor"></a>Współautor danych obiektu blob usługi Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Odczytuj, zapisuj i usuwaj kontenery i obiekty blob usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Identyfikator** | ba92f5b4-2d11-453d-a403-e96b0029c9fe |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/usuwanie | Usuń kontener. |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/odczyt | Zwraca kontener lub listę kontenerów. |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/zapis | Modyfikowanie metadanych lub właściwości kontenera. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/usuwanie | Usuwanie obiektu blob. |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/odczyt | Zwraca obiekt BLOB lub listę obiektów BLOB. |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/zapis | Zapisz w obiekcie blob. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-blob-data-owner"></a>Właściciel danych obiektu blob usługi Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zapewnia pełen dostęp do kontenerów obiektów blob i danych usługi Azure Storage, w tym do przypisywania kontroli dostępu POSIX. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Identyfikator** | b7e6dc6d-f1e8-4753-8033-0f276bb0955b |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/* | Pełne uprawnienia do kontenerów. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/* | Pełne uprawnienia do obiektów BLOB. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-blob-data-reader"></a>Czytelnik danych obiektu blob usługi Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Odczytuj i wyświetlaj kontenery usługi Azure Storage oraz obiekty blob. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Identyfikator** | 2a2b9908-6ea1-4ae2-8e65-a410df84e7d1 |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/odczyt | Zwraca kontener lub listę kontenerów. |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/blobServices/kontenery/obiekty blob/odczyt | Zwraca obiekt BLOB lub listę obiektów BLOB. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-blob-delegator"></a>Delegat obiektów blob magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Pobierz klucz delegowania użytkownika, którego można następnie użyć do utworzenia sygnatury dostępu współdzielonego dla kontenera lub obiektu BLOB podpisanego przy użyciu poświadczeń usługi Azure AD. Aby uzyskać więcej informacji, zobacz [Tworzenie skojarzeń zabezpieczeń dla delegowania użytkowników](https://docs.microsoft.com/rest/api/storageservices/create-user-delegation-sas). |
> | **Identyfikator** | db58b8e5-c6ad-4a2a-8342-4190687cbf4a |
> | **Akcje** |  |
> | Microsoft.Storage/storageAccounts/blobServices/generateUserDelegationKey/action | Zwraca klucz delegowania użytkownika dla Blob service. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-file-data-smb-share-contributor"></a>Współautor udziałów SMB danych plików magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na dostęp do odczytu, zapisu i usuwania w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB |
> | **Identyfikator** | 0c867c2a-1d8c-454a-a3db-ab2ea1bdc8bb |
> | **Akcje** |  |
> | *dawaj* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Zwraca plik/folder lub listę plików/folderów. |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/zapis | Zwraca wynik zapisania pliku lub utworzenia folderu. |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/usuwanie | Zwraca wynik usunięcia pliku/folderu. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-file-data-smb-share-elevated-contributor"></a>Współautor współautora udziału SMB danych plików magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na dostęp do odczytu, zapisu, usuwania i modyfikowania uprawnień NTFS w udziałach plików usługi Azure Storage za pośrednictwem protokołu SMB |
> | **Identyfikator** | a7264617-510b-434b-a828-9731dc254ea7 |
> | **Akcje** |  |
> | *dawaj* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Zwraca plik/folder lub listę plików/folderów. |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/zapis | Zwraca wynik zapisania pliku lub utworzenia folderu. |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/pliki/usuwanie | Zwraca wynik usunięcia pliku/folderu. |
> | Microsoft. Storage/storageAccounts/fileServices/udziałów plików/Files/modifypermissions/Action | Zwraca wynik modyfikacji uprawnienia do pliku/folderu. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-file-data-smb-share-reader"></a>Czytnik udziałów SMB danych plików magazynu
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Zezwala na dostęp do odczytu do udziału plików platformy Azure za pośrednictwem protokołu SMB |
> | **Identyfikator** | aba4ae5f-2193-4029-9191-0cb91df5e314 |
> | **Akcje** |  |
> | *dawaj* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft.Storage/storageAccounts/fileServices/fileshares/files/read | Zwraca plik/folder lub listę plików/folderów. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-queue-data-contributor"></a>Współautor danych kolejki usługi Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Odczytuj, zapisuj i usuwaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Identyfikator** | 974c5e8b-45b9-4653-ba55-5f855dd0fb88 |
> | **Akcje** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Delete | Usuń kolejkę. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Zwróć kolejkę lub listę kolejek. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/Write | Modyfikuj metadane lub właściwości kolejki. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Delete | Usuń co najmniej jeden komunikat z kolejki. |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Wgląd lub pobranie co najmniej jednego komunikatu z kolejki. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Write | Dodaj komunikat do kolejki. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-queue-data-message-processor"></a>Procesor komunikatów danych kolejki usługi Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Wgląd, pobieranie i usuwanie wiadomości z kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Identyfikator** | 8a0f0c08-91a1-4084-bc3d-661d67233fed |
> | **Akcje** |  |
> | *dawaj* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Wgląd do wiadomości. |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Process/Action | Pobierz i Usuń komunikat. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-queue-data-message-sender"></a>Nadawca komunikatów danych kolejki usługi Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Dodawanie komunikatów do kolejki usługi Azure Storage. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Identyfikator** | c6a89b2d-59bc-44d0-9896-0f6e12d7b80a |
> | **Akcje** |  |
> | *dawaj* |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft. Storage/storageAccounts/queueServices/Queues/messages/Add/Action | Dodaj komunikat do kolejki. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="storage-queue-data-reader"></a>Czytelnik danych kolejki usługi Storage
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Odczytuj i wyświetlaj kolejki usługi Azure Storage oraz wiadomości w kolejce. Aby dowiedzieć się, które akcje są wymagane dla danej operacji danych, zobacz [uprawnienia do wywoływania operacji na danych obiektów blob i kolejek](https://docs.microsoft.com/rest/api/storageservices/authenticate-with-azure-active-directory#permissions-for-calling-blob-and-queue-data-operations). |
> | **Identyfikator** | 19e7f393-937e-4f77-808e-94535e297925 |
> | **Akcje** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/read | Zwraca kolejkę lub listę kolejek. |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft.Storage/storageAccounts/queueServices/queues/messages/read | Wgląd lub pobranie co najmniej jednego komunikatu z kolejki. |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="support-request-contributor"></a>Współautor wniosku o pomoc techniczną
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Pozwala tworzyć żądania pomocy technicznej i zarządzać nimi |
> | **Identyfikator** | cfd33db0-3dd1-45e3-aa9d-cdbdf3b6f24e |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Autoryzacja odczytu |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="traffic-manager-contributor"></a>Współautor usługi Traffic Manager
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie profilami Traffic Manager, ale nie pozwala na kontrolowanie użytkowników, którzy mają do nich dostęp. |
> | **Identyfikator** | a4b10055-b0c7-44c2-b00f-c7b5b3550cf7 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Odczytuj role i przypisania ról |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Network/trafficManagerProfiles/* |  |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="user-access-administrator"></a>Administrator dostępu użytkowników
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie dostępem użytkowników do zasobów platformy Azure. |
> | **Identyfikator** | 18d7d88d-d35e-4fb5-a5c3-7773c20a72d9 |
> | **Akcje** |  |
> | */read | Odczytuj zasoby wszystkich typów, z wyjątkiem kluczy tajnych. |
> | Microsoft.Authorization/* | Zarządzanie autoryzacją |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="virtual-machine-administrator-login"></a>Logowanie administratora maszyny wirtualnej
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Wyświetl Virtual Machines w portalu i zaloguj się jako administrator |
> | **Identyfikator** | 1c0163c0-47e6-4577-8991-ea5c82e286e4 |
> | **Akcje** |  |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.Network/loadBalancers/read | Pobiera definicję modułu równoważenia obciążenia |
> | Microsoft.Network/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | Microsoft.Compute/virtualMachines/loginAsAdmin/action | Logowanie się do maszyny wirtualnej przy użyciu uprawnień administratora systemu Windows lub użytkownika root w systemie Linux |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="virtual-machine-contributor"></a>Współautor maszyny wirtualnej
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie maszynami wirtualnymi, ale nie dostęp do nich, a nie do sieci wirtualnej ani konta magazynu, z którymi są połączone. |
> | **Identyfikator** | 9980e02c-c2be-4d73-94e8-173b1dc7cf3c |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Autoryzacja odczytu |
> | Microsoft.Compute/availabilitySets/* | Tworzenie zestawów dostępności obliczeń i zarządzanie nimi |
> | Microsoft.Compute/locations/* | Tworzenie lokalizacji obliczeniowych i zarządzanie nimi |
> | Microsoft.Compute/virtualMachines/* | Tworzenie maszyn wirtualnych i zarządzanie nimi |
> | Microsoft.Compute/virtualMachineScaleSets/* | Tworzenie zestawów skalowania maszyn wirtualnych i zarządzanie nimi |
> | Microsoft.DevTestLab/schedules/* |  |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Network/applicationGateways/backendAddressPools/join/action | Sprzęga pulę adresów zaplecza bramy aplikacji. Brak alertów. |
> | Microsoft.Network/loadBalancers/backendAddressPools/join/action | Sprzęga pulę adresów zaplecza modułu równoważenia obciążenia. Brak alertów. |
> | Microsoft.Network/loadBalancers/inboundNatPools/join/action | Przyłącza do puli NAT dla ruchu przychodzącego modułu równoważenia obciążenia. Brak alertów. |
> | Microsoft.Network/loadBalancers/inboundNatRules/join/action | Przyłącza do przychodzącej reguły NAT modułu równoważenia obciążenia. Brak alertów. |
> | Microsoft.Network/loadBalancers/probes/join/action | Umożliwia korzystanie z sond modułu równoważenia obciążenia. Na przykład za pomocą tej właściwości uprawnienia healthProbe zestawu skalowania maszyn wirtualnych można odwoływać się do sondy. Brak alertów. |
> | Microsoft.Network/loadBalancers/read | Pobiera definicję modułu równoważenia obciążenia |
> | Microsoft.Network/locations/* | Tworzenie lokalizacji sieciowych i zarządzanie nimi |
> | Microsoft.Network/networkInterfaces/* | Tworzenie interfejsów sieciowych i zarządzanie nimi |
> | Microsoft.Network/networkSecurityGroups/join/action | Przyłącza do sieciowej grupy zabezpieczeń. Brak alertów. |
> | Microsoft.Network/networkSecurityGroups/read | Pobiera definicję sieciowej grupy zabezpieczeń |
> | Microsoft.Network/publicIPAddresses/join/action | Przyłącza do publicznego adresu IP. Brak alertów. |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.Network/virtualNetworks/subnets/join/action | Przyłącza do sieci wirtualnej. Brak alertów. |
> | Microsoft.RecoveryServices/locations/* |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/backupProtectionIntent/write | Utwórz opcję ochrony kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/*/read |  |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | Zwraca szczegóły obiektu chronionego elementu |
> | Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | Utwórz chroniony element kopii zapasowej |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/read | Zwraca wszystkie zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/backupPolicies/write | Tworzy zasady ochrony |
> | Microsoft.RecoveryServices/Vaults/read | Operacja Pobierz magazyn pobiera obiekt reprezentujący zasób platformy Azure typu "magazyn" |
> | Microsoft.RecoveryServices/Vaults/usages/read | Zwraca szczegóły użycia magazynu usług Recovery Services. |
> | Microsoft.RecoveryServices/Vaults/write | Operacja Utwórz magazyn tworzy zasób platformy Azure typu „magazyn” |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft. SqlVirtualMachine/* |  |
> | Microsoft.Storage/storageAccounts/listKeys/action | Zwraca klucze dostępu dla określonego konta magazynu. |
> | Microsoft.Storage/storageAccounts/read | Zwraca listę kont magazynu lub pobiera właściwości dla określonego konta magazynu. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="virtual-machine-user-login"></a>Logowanie użytkownika maszyny wirtualnej
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Wyświetl Virtual Machines w portalu i zaloguj się jako zwykły użytkownik. |
> | **Identyfikator** | fb879df8-f326-4884-b1cf-06f3ad86be52 |
> | **Akcje** |  |
> | Microsoft.Network/publicIPAddresses/read | Pobiera definicję publicznego adresu IP. |
> | Microsoft.Network/virtualNetworks/read | Pobierz definicję sieci wirtualnej |
> | Microsoft.Network/loadBalancers/read | Pobiera definicję modułu równoważenia obciążenia |
> | Microsoft.Network/networkInterfaces/read | Pobiera definicję interfejsu sieciowego.  |
> | Microsoft.Compute/virtualMachines/*/read |  |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | Microsoft.Compute/virtualMachines/login/action | Zaloguj się do maszyny wirtualnej jako zwykły użytkownik |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="web-plan-contributor"></a>Współautor planów sieci Web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie planami sieci Web dla witryn internetowych, ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | 2cc479cb-7b4d-49a8-b449-8c00fd0f0a4b |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Autoryzacja odczytu |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Web/serverFarms/* | Tworzenie farm serwerów i zarządzanie nimi |
> | Microsoft. Web/hostingEnvironments/Join/Action | Sprzęga App Service Environment |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="website-contributor"></a>Współautor witryny sieci Web
> [!div class="mx-tableFixed"]
> | | |
> | --- | --- |
> | **Opis** | Umożliwia zarządzanie witrynami sieci Web (nie planami internetowymi), ale nie umożliwia uzyskiwania do nich dostępu. |
> | **Identyfikator** | de139f84-1756-47ae-9be6-808fbbe84772 |
> | **Akcje** |  |
> | Microsoft.Authorization/*/read | Autoryzacja odczytu |
> | Microsoft.Insights/alertRules/* | Tworzenie reguł alertów usługi Insights i zarządzanie nimi |
> | Microsoft.Insights/components/* | Tworzenie składników usługi Insights i zarządzanie nimi |
> | Microsoft.ResourceHealth/availabilityStatuses/read | Pobiera stan dostępności dla wszystkich zasobów w określonym zakresie |
> | Microsoft. resources/Deployments/* | Tworzenie wdrożeń grup zasobów i zarządzanie nimi |
> | Microsoft.Resources/subscriptions/resourceGroups/read | Pobiera lub wyświetla listę grup zasobów. |
> | Microsoft.Support/* | Tworzenie biletów pomocy technicznej i zarządzanie nimi |
> | Microsoft.Web/certificates/* | Tworzenie i zarządzanie certyfikatami witryny sieci Web |
> | Microsoft.Web/listSitesAssignedToHostName/read | Pobieranie nazw lokacji przypisanych do nazwy hosta. |
> | Microsoft.Web/serverFarms/join/action |  |
> | Microsoft.Web/serverFarms/read | Pobierz właściwości planu App Service |
> | Microsoft.Web/sites/* | Tworzenie witryn sieci Web i zarządzanie nimi (Tworzenie witryn wymaga również uprawnień do zapisu w skojarzonym planie App Service) |
> | **Nonaruszone** |  |
> | *dawaj* |  |
> | **Akcje dataactions** |  |
> | *dawaj* |  |
> | **NotDataActions** |  |
> | *dawaj* |  |

## <a name="next-steps"></a>Następne kroki

- [Niestandardowe role dla zasobów platformy Azure](custom-roles.md)
- [Zarządzanie dostępem do zasobów platformy Azure za pomocą kontroli dostępu opartej na rolach i witryny Azure Portal](role-assignments-portal.md)
- [Uprawnienia w usłudze Azure Security Center](../security-center/security-center-permissions.md)
