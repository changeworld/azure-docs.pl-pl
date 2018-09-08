---
title: Przegląd zabezpieczeń w Data Lake Store | Dokumentacja firmy Microsoft
description: Dowiedz się, jak usługi Azure Data Lake Store jest bardziej bezpiecznego magazynu danych big data
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ebd5b2ac-c5cc-46d4-9cfd-1a1ee70024c2
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2018
ms.author: nitinme
ms.openlocfilehash: 4ecc94f4ab5e9091db1705e99d4a5df6abbaf350
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/07/2018
ms.locfileid: "44161088"
---
# <a name="security-in-azure-data-lake-store"></a>Zabezpieczenia w usłudze Azure Data Lake Store
Wiele przedsiębiorstw są się korzystanie z analizy danych big data, aby uzyskać szczegółowe informacje biznesowe pomóc im inteligentne decyzje. Organizacja może być skomplikowane i podlegających regulacjom środowiska z coraz większą liczbę różnych użytkowników. Ważne jest dla przedsiębiorstw upewnić się, że kluczowe dane biznesowe są przechowywane w bardziej bezpiecznie z odpowiedniego poziomu dostępu przyznane dla poszczególnych użytkowników. Azure Data Lake Store ułatwia spełnienie tych wymagań dotyczących zabezpieczeń. Ten artykuł zawiera informacje na temat możliwości zabezpieczeń Data Lake Store, w tym:

* Authentication
* Autoryzacja
* Izolacja sieci
* Ochrona danych
* Inspekcja

## <a name="authentication-and-identity-management"></a>Uwierzytelnianie i zarządzania tożsamościami
Uwierzytelnianie jest procesem, za pomocą którego tożsamości użytkownika została zweryfikowana, gdy użytkownik wchodzi w interakcję z Data Lake Store lub w połączeniu z dowolną usługą, który nawiązuje połączenie z Data Lake Store. Dla zarządzania tożsamościami i uwierzytelniania usługa Data Lake Store używa [usługi Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md), kompleksowe tożsamość i dostęp do chmury rozwiązanie do zarządzania, która upraszcza zarządzanie użytkownikami i grupami.

Każda subskrypcja platformy Azure może być skojarzony z wystąpieniem usługi Azure Active Directory. Tylko użytkownicy i tożsamości usługi, które są zdefiniowane w usłudze Azure Active Directory można uzyskać dostępu do konta Data Lake Store przy użyciu witryny Azure portal, narzędzi wiersza polecenia lub za pośrednictwem aplikacji klienckich organizacji tworzy się przy użyciu usługi Azure Data Lake Store zestawu SDK. Dostępne są następujące kluczowe korzyści wynikające z używania usługi Azure Active Directory jako mechanizmu kontroli dostępu w scentralizowany:

* Uproszczone zarządzanie cyklem życia tożsamości. Tożsamość użytkownika lub usługi (tożsamość jednostki usługi) można szybko tworzyć i szybko odwołany, po prostu usunięcie lub wyłączenie konta w katalogu.
* Uwierzytelnianie wieloskładnikowe. [Uwierzytelnianie wieloskładnikowe](../active-directory/authentication/multi-factor-authentication.md) zapewnia dodatkową warstwę zabezpieczeń dla sesji logowania użytkowników i transakcji.
* Uwierzytelnianie za pomocą dowolnego klienta za pośrednictwem standardowego protokołu open, takich jak OAuth lub OpenID.
* Federacja z usługami katalogu przedsiębiorstwa i dostawcy tożsamości w chmurze.

## <a name="authorization-and-access-control"></a>Autoryzacji i kontroli dostępu
Po usłudze Azure Active Directory uwierzytelnia użytkownika, dzięki czemu będzie można uzyskać dostęp do usługi Azure Data Lake Store, kontrolek autoryzacji uprawnień dostępu dla programu Data Lake Store. Data Lake Store oddziela autoryzacji działań związanych z kontem i związanych z danymi w następujący sposób:

* [Kontrola dostępu oparta na rolach](../role-based-access-control/overview.md) (RBAC) udostępnianej przez platformę Azure w celu zarządzania kontami
* Listy ACL modelu POSIX w celu uzyskania dostępu do danych w magazynie

### <a name="rbac-for-account-management"></a>RBAC dla konta zarządzania
Cztery podstawowe role są definiowane dla Data Lake Store domyślnie. Role zezwalać na różnych operacji na koncie przy użyciu witryny Azure portal, poleceń cmdlet programu PowerShell i interfejsów API REST usługi Data Lake Store. Właściciel i współautor role można wykonać szereg funkcji administracyjnych na koncie. Rola czytelnika można przypisać do użytkowników, którzy wyświetlają tylko dane zarządzania kontem.

![Role RBAC](./media/data-lake-store-security-overview/rbac-roles.png "role RBAC")

Należy pamiętać, że chociaż ról przypisanych do zarządzania kontami, niektóre role mają wpływ na dostęp do danych. Należy użyć listy ACL do kontrolowania dostępu do operacji, które użytkownik może wykonywać w systemie plików. W poniższej tabeli przedstawiono podsumowanie praw do zarządzania i prawa dostępu do danych na domyślne role.

| Role | Uprawnienia do zarządzania | Prawa dostępu do danych | Wyjaśnienie |
| --- | --- | --- | --- |
| Nie przypisanej roli. |Brak |Regulowane przez listy kontroli dostępu |Użytkownik nie może używać witryny Azure portal lub poleceń cmdlet programu Azure PowerShell do przeglądania Data Lake Store. Użytkownik może użyć tylko narzędzia wiersza polecenia. |
| Właściciel |Wszyscy |Wszyscy |Rola właściciela jest administratora. Tej roli mogą zarządzać wszystkim i ma pełny dostęp do danych. |
| Czytelnik |Tylko do odczytu |Regulowane przez listy kontroli dostępu |Rola Czytelnik może przeglądać wszystko dotyczące zarządzania kontem, takich jak której użytkownik jest przypisany do roli. Rola czytelnika nie może wprowadzać żadnych zmian. |
| Współautor |Wszystkie regiony z wyjątkiem dodawania i usuwania ról |Regulowane przez listy kontroli dostępu |Do roli Współautor mogą zarządzać pewnymi aspektami konta, takie jak wdrożeń oraz tworzenie i Zarządzanie alertami. Rola Współautor nie mogą dodawać i usuwać role. |
| Administrator dostępu użytkowników |Dodawanie i usuwanie ról |Regulowane przez listy kontroli dostępu |Rola Administrator dostępu użytkowników można zarządzać dostępem użytkowników do kont. |

Aby uzyskać instrukcje, zobacz [przypisywanie użytkowników lub grup zabezpieczeń do konta Data Lake Store](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Za pomocą list ACL dla operacji na systemy plików
Data Lake Store jest hierarchiczny system plików takich jak Hadoop Distributed pliku System (HDFS) i obsługuje [listy ACL modelu POSIX](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Kontroluje Odczyt (r), zapis (w) i wykonanie (x) uprawnienia do zasobów dla roli właściciel, właściciele grupy i dla innych użytkowników i grup. W Data Lake Store można włączyć listy ACL dla folderu głównego, podfolderów i poszczególnych plików. Aby uzyskać więcej informacji na temat sposobu działania list kontroli dostępu w kontekście usługi Data Lake Store, zobacz [Kontrola dostępu w usłudze Data Lake Store](data-lake-store-access-control.md).

Firma Microsoft zaleca, definiować listy ACL dla wielu użytkowników za pomocą [grup zabezpieczeń](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md). Dodawanie użytkowników do grupy zabezpieczeń, a następnie przypisz listy ACL dla pliku lub folderu do tej grupy zabezpieczeń. Jest to przydatne, gdy chcesz udostępnić przypisanych uprawnień, ponieważ są ograniczone do maksymalnie 28 wpisów dla przypisanych uprawnień. Aby uzyskać więcej informacji o tym, jak lepiej zabezpieczyć dane przechowywane w Data Lake Store przy użyciu grup zabezpieczeń usługi Azure Active Directory, zobacz [przypisywanie użytkowników lub grupy zabezpieczeń jako list ACL do systemu plików usługi Azure Data Lake Store](data-lake-store-secure-data.md#filepermissions).

![Listy uprawnień dostępu](./media/data-lake-store-security-overview/adl.acl.2.png "uprawnienia dostępu do listy")

## <a name="network-isolation"></a>Izolacja sieci
Użyj Data Lake Store aby ułatwić sterowanie dostępem do magazynu danych na poziomie sieci. Można określić zapory i określić zakres adresów IP z zaufanych klientów. Zakres adresów IP tylko klienci, którzy mają adresy IP ze zdefiniowanego zakresu mogą się łączyć z Data Lake Store.

![Dostęp do ustawień zapory i IP](./media/data-lake-store-security-overview/firewall-ip-access.png "zapory adresów IP i ustawienia")

## <a name="data-protection"></a>Ochrona danych
Azure Data Lake Store zapewnia ochronę danych w całym cyklu życia. Do przesyłania danych Data Lake Store używa standardowego w branży protokołu Transport Layer Security (TLS 1.2), aby chronić dane za pośrednictwem sieci.

![Szyfrowanie w Data Lake Store](./media/data-lake-store-security-overview/adls-encryption.png "szyfrowania w Data Lake Store")

Usługa Data Lake Store umożliwia także szyfrowanie danych przechowywanych w ramach konta. Możesz wybrać szyfrowanie danych lub jego brak. Jeśli postanowisz w przypadku szyfrowania danych przechowywanych w Data Lake Store są szyfrowane przed zapisaniem na nośniku trwałym. W takim przypadku Data Lake Store automatycznie szyfruje dane przed utrwaleniem i odszyfrowuje przed pobraniem, dzięki czemu jest całkowicie niewidoczne dla klienta, dostęp do danych. Nie nastąpiła żadna zmiana kodu, wymagane po stronie klienta do szyfrowania/odszyfrowywania danych.

Do zarządzania kluczami Data Lake Store udostępnia dwa tryby zarządzania kluczami szyfrowania głównego (głównymi kluczami szyfrowania), które są wymagane w celu odszyfrowania żadnych danych, która jest przechowywana w Data Lake Store. Można albo pozwolić Data Lake Store zarządzania głównymi kluczami szyfrowania lub zachowanych własności głównymi kluczami szyfrowania za pomocą konta usługi Azure Key Vault. Tryb zarządzania kluczami należy określić podczas tworzenia konta Data Lake Store. Aby uzyskać więcej informacji na temat sposobu zapewnienia konfiguracji odnoszącej się do szyfrowania, zobacz [Rozpoczynanie pracy z usługą Azure Data Lake Store za pomocą witryny Azure Portal](data-lake-store-get-started-portal.md).

## <a name="activity-and-diagnostic-logs"></a>Aktywności i dzienników diagnostycznych
Można użyć działania lub dzienniki diagnostyczne, w zależności od tego, czy szukasz dzienniki działań związanych z zarządzaniem konta lub działań związanych z danymi.

* Konta działań związanych z zarządzaniem Użyj interfejsów API usługi Azure Resource Manager i są udostępniane w witrynie Azure portal za pomocą dzienników aktywności.
* Działania związane z danymi przy użyciu interfejsów API REST WebHDFS i są udostępniane w witrynie Azure portal za pomocą dzienników diagnostycznych.

### <a name="activity-log"></a>Dziennik aktywności
W celu zachowania zgodności z przepisami, organizacja może wymagać dziennika inspekcji odpowiednie dla konta działania związane z zarządzaniem wymaga analizując określone zdarzenia. Data Lake Store ma wbudowane monitorowanie i rejestruje wszystkie działania związane z zarządzaniem kontem.

Konto zarządzania ścieżki inspekcyjne wyświetlić i wybrać kolumny, które mają być rejestrowane. Można również wyeksportować dzienników aktywności do usługi Azure Storage.

![Dziennik aktywności](./media/data-lake-store-security-overview/activity-logs.png "dziennika aktywności")

Aby uzyskać więcej informacji na temat pracy z dziennikami aktywności, zobacz [wyświetlanie dzienników aktywności do inspekcji akcje na zasobach](../azure-resource-manager/resource-group-audit.md).

### <a name="diagnostics-logs"></a>Dzienniki diagnostyczne
Można włączyć inspekcji dostępu do danych i rejestrowanie diagnostyczne w witrynie Azure portal i wysłać dzienniki do konta usługi Azure Blob storage, Centrum zdarzeń lub usługi Log Analytics.

![Dzienniki diagnostyczne](./media/data-lake-store-security-overview/diagnostic-logs.png "dzienniki diagnostyczne")

Aby uzyskać więcej informacji na temat pracy z dzienników diagnostycznych za pomocą usługi Azure Data Lake Store, zobacz [uzyskiwania dostępu do dzienników diagnostycznych usługi Data Lake Store](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Podsumowanie
Klienci korporacyjni popytu platformy chmury analizy danych, które jest bezpieczne i łatwe w użyciu. Azure Data Lake Store jest zaprojektowane w celu rozwiązania tych wymagań przy użyciu zarządzania tożsamościami i uwierzytelniania za pomocą integracji usługi Azure Active Directory, autoryzacja na podstawie listy ACL, izolacji sieci, szyfrowanie danych przesyłanych i magazynowanych i inspekcji.

Jeśli chcesz zobaczyć nowe funkcje programu Data Lake Store, Wyślij do nas swoją opinię [forum UserVoice programu Data Lake Store](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Zobacz także
* [Omówienie usługi Azure Data Lake Store](data-lake-store-overview.md)
* [Rozpoczynanie pracy z usługą Data Lake Store](data-lake-store-get-started-portal.md)
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)

