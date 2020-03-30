---
title: Role i wymagania dotyczące usługi Azure Data Share
description: Dowiedz się więcej o uprawnieniach wymaganych do udostępniania i odbierania danych przy użyciu usługi Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265508"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Role i wymagania dotyczące usługi Azure Data Share 

W tym artykule opisano role i uprawnienia wymagane do udostępniania i odbierania danych przy użyciu usługi Azure Data Share. 

## <a name="roles-and-requirements"></a>Role i wymagania

Dzięki usłudze Azure Data Share można udostępniać dane bez wymiany poświadczeń między dostawcą danych a konsumentem. Usługa Azure Data Share używa tożsamości zarządzanych (wcześniej nazywanych MSI) do uwierzytelniania w magazynie danych platformy Azure. 

Tożsamość zarządzana zasobu usługi Azure Data Share musi mieć dostęp do magazynu danych platformy Azure. Usługa Azure Data Share używa tej tożsamości zarządzanej do odczytu i zapisu danych do udostępniania opartego na migawkach oraz do ustanowienia łącza symbolicznego do udostępniania w miejscu. 

Aby udostępnić lub odbierać dane z magazynu danych platformy Azure, użytkownik potrzebuje co najmniej następujących uprawnień. Dodatkowe uprawnienia są wymagane dla udostępniania opartego na języku SQL.
* Uprawnienie do zapisywania w magazynie danych platformy Azure. Zazwyczaj to uprawnienie istnieje w roli **współautora.**
* Uprawnienie do tworzenia przypisania ról w magazynie danych platformy Azure. Zazwyczaj uprawnienia do tworzenia przypisań ról istnieje w roli **Właściciela,** Administrator dostępu użytkownika lub roli niestandardowej z przypisaniem microsoft.Autoryzacja/przypisania ról/zapis przypisania. To uprawnienie nie jest wymagane, jeśli tożsamość zarządzana zasobu udziału danych ma już przyznany dostęp do magazynu danych platformy Azure. W poniższej tabeli znajduje się wymagana rola.

Poniżej znajduje się podsumowanie ról przypisanych do tożsamości zarządzanej zasobu udziału danych:

| |  |  |
|---|---|---|
|**Typ magazynu danych**|**Magazyn danych źródła dostawcy danych**|**Docelowy magazyn danych konsumenta danych**|
|Azure Blob Storage| Czytnik danych obiektów blob magazynu | Współautor danych obiektów blob magazynu
|Azure Data Lake Gen1 | Właściciel | Nieobsługiwane
|Azure Data Lake Gen2 | Czytnik danych obiektów blob magazynu | Współautor danych obiektów blob magazynu
|Azure SQL Server | Współautor bazy danych SQL | Współautor bazy danych SQL
|Klaster eksploratora danych platformy Azure | Współautor | Współautor
|

W przypadku udostępniania opartego na języku SQL użytkownik SQL musi zostać utworzony z zewnętrznego dostawcy w bazie danych SQL o takiej samej nazwie jak zasób usługi Azure Data Share. Poniżej znajduje się podsumowanie uprawnień wymaganych przez użytkownika SQL.

| |  |  |
|---|---|---|
|**Typ bazy danych SQL**|**Uprawnienie użytkownika SQL dostawcy danych**|**Uprawnienia użytkownika SQL dla konsumenta danych**|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics (dawniej SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|


### <a name="data-provider"></a>Dostawca danych 
Aby dodać zestaw danych w usłudze Azure Data Share, tożsamość zarządzana zasobu udziału danych dostawcy musi mieć dostęp do źródłowego magazynu danych platformy Azure. Na przykład w przypadku konta magazynu, zasobów zarządzanych udziału danych tożsamości jest przyznawana roli czytnika danych obiektu blob magazynu. 

Odbywa się to automatycznie przez usługę Azure Data Share, gdy użytkownik dodaje zestaw danych za pośrednictwem witryny Azure portal, a użytkownik ma odpowiednie uprawnienia. Na przykład użytkownik jest właścicielem magazynu danych platformy Azure lub jest członkiem roli niestandardowej, która ma przypisanie microsoft.Authorization/role assignments/write. 

Alternatywnie użytkownik może mieć właściciela magazynu danych platformy Azure dodać zasobów zasobów udziału danych tożsamości zarządzanej ręcznie do magazynu danych platformy Azure. Ta akcja musi być wykonana tylko raz na zasób udziału danych.

Aby utworzyć przypisanie roli dla tożsamości zarządzanej zasobu udziału danych, wykonaj poniższe czynności:

1. Przejdź do magazynu danych platformy Azure.
1. Wybierz **pozycję Kontrola dostępu (IAM)**.
1. Wybierz **pozycję Dodaj przypisanie roli**.
1. W obszarze *Rola*wybierz rolę w powyższej tabeli przypisania roli (na przykład dla konta magazynu wybierz czytnik *danych obiektów blob magazynu).*
1. W obszarze *Wybierz*wpisz nazwę zasobu udziału danych platformy Azure.
1. Kliknij przycisk *Zapisz*.

W przypadku źródeł opartych na języku SQL, oprócz powyższych kroków użytkownik SQL musi zostać utworzony z zewnętrznego dostawcy w bazie danych SQL o takiej samej nazwie jak zasób usługi Azure Data Share. Ten użytkownik musi mieć db_datareader *uprawnienia.* Przykładowy skrypt wraz z innymi wymaganiami wstępnymi udostępniania opartego na języku SQL można znaleźć w [samouczku udostępniania danych.](share-your-data.md) 

### <a name="data-consumer"></a>Konsument danych
Aby odbierać dane, zasobów zarządzanych udziału danych konsumenta musi mieć dostęp do docelowego magazynu danych platformy Azure. Na przykład w przypadku konta magazynu, zasobów zarządzanych udziału danych tożsamości jest przyznawana roli współautor danych obiektu blob magazynu. 

Odbywa się to automatycznie przez usługę Azure Data Share, jeśli użytkownik określa docelowy magazyn danych za pośrednictwem witryny Azure portal, a użytkownik ma odpowiednie uprawnienia. Na przykład użytkownik jest właścicielem magazynu danych platformy Azure lub jest członkiem roli niestandardowej, która ma przypisanie microsoft.Authorization/role assignments/write. 

Alternatywnie użytkownik może mieć właściciela magazynu danych platformy Azure dodać zasobów zasobów udziału danych tożsamości zarządzanej ręcznie do magazynu danych platformy Azure. Ta akcja musi być wykonana tylko raz na zasób udziału danych.

Aby ręcznie utworzyć przypisanie roli dla tożsamości zarządzanej zasobu udziału danych, wykonaj poniższe czynności:

1. Przejdź do magazynu danych platformy Azure.
1. Wybierz **pozycję Kontrola dostępu (IAM)**.
1. Wybierz **pozycję Dodaj przypisanie roli**.
1. W obszarze *Rola*wybierz rolę w powyższej tabeli przypisania roli (na przykład dla konta magazynu wybierz czytnik *danych obiektów blob magazynu).*
1. W obszarze *Wybierz*wpisz nazwę zasobu udziału danych platformy Azure.
1. Kliknij przycisk *Zapisz*.

W przypadku obiektu docelowego opartego na języku SQL, oprócz powyższych kroków użytkownik SQL musi zostać utworzony z zewnętrznego dostawcy w bazie danych SQL o takiej samej nazwie jak zasób udostępniania danych platformy Azure. Ten użytkownik musi mieć *db_datareader, db_datawriter, db_ddladmin* uprawnienia. Przykładowy skrypt wraz z innymi wymaganiami wstępnymi udostępniania opartego na języku SQL można znaleźć w samouczku [akceptowania i odbierania danych.](subscribe-to-data-share.md) 

Jeśli udostępniasz dane przy użyciu interfejsów API REST, musisz ręcznie utworzyć te przypisania ról. 

Aby dowiedzieć się więcej o dodaniu przypisania roli, zapoznaj się z [tą dokumentacją,](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). 

## <a name="resource-provider-registration"></a>Rejestracja dostawcy zasobów 

Aby wyświetlić zaproszenie do udostępniania danych platformy Azure po raz pierwszy w dzierżawie platformy Azure, może być konieczne ręczne zarejestrowanie dostawcy zasobów Microsoft.DataShare w ramach subskrypcji platformy Azure. Wykonaj następujące kroki, aby zarejestrować dostawcę zasobów Microsoft.DataShare w ramach subskrypcji platformy Azure. Potrzebujesz dostępu *współautora* do subskrypcji platformy Azure, aby zarejestrować dostawcę zasobów.

1. W witrynie Azure portal przejdź do **pozycji Subskrypcje**.
1. Wybierz subskrypcję, której używasz do udostępniania danych platformy Azure.
1. Kliknij **dostawców zasobów**.
1. Wyszukaj witrynę Microsoft.DataShare.
1. Kliknij **pozycję Zarejestruj**.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o rolach na platformie Azure — [poznaj definicje ról](../role-based-access-control/role-definitions.md)

