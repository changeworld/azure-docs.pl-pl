---
title: Dodawanie użytkowników do konta usługi Azure Data Lake Analytics
description: Dowiedz się, jak poprawnie Dodawanie użytkowników do swojego konta usługi Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 58b78c7d9769069f36c9f01c2a7650878a6c5ec9
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/04/2018
ms.locfileid: "34717232"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Dodanie użytkownika w portalu Azure

## <a name="start-the-add-user-wizard"></a>Uruchom Kreator dodawania użytkownika
1. Otwórz z usługi Azure Data Lake Analytics za pomocą https://portal.azure.com.
2. Kliknij przycisk **Kreator dodawania użytkownika**.
3. W **wybierz użytkownika** kroku, Znajdź użytkownika, które chcesz dodać. Kliknij pozycję **Wybierz**.
4. **wybierz rolę** kroku, wybierz **Data Lake Analytics Developer**. Ta rola ma minimalny zestaw uprawnień wymaganych do przesyłania/monitorowania/zarządzania zadań U-SQL. Jeśli grupa nie jest przeznaczona do zarządzania usługami Azure, należy przypisać do tej roli.
5. W **wybierz uprawnienia katalogu** kroku, wybierz żadnych dodatkowych baz danych wymaga dostępu do tego użytkownika. Odczytu i zapisu do bazy danych master jest wymagany do przesyłania zadań. Gdy skończysz, kliknij przycisk **OK**.
6. W ostatnim kroku o nazwie **przypisać uprawnienia wybranego** Przejrzyj zmiany spowoduje, że kreator. Kliknij przycisk **OK**.


## <a name="configure-acls-for-data-folders"></a>Skonfigurować listy kontroli dostępu dla danych folderów
Przyznaj "R-X" lub "RWX", w razie potrzeby, folderów zawierających dane wejściowe i dane wyjściowe.


## <a name="optionally-add-the-user-to-the-azure-data-lake-store-role-reader-role"></a>Opcjonalnie można dodać użytkownika do roli usługi Azure Data Lake Store **czytnika** roli.
1.  Znajdź konto usługi Azure Data Lake Store.
2.  Polecenie **użytkowników**.
3. Kliknij pozycję **Add** (Dodaj).
4.  Wybierz rolę Azure RBAC można przypisać do tej grupy.
5.  Przypisać rolę czytelnika. Ta rola ma minimalny zestaw uprawnień wymaganych do przeglądania lub zarządzać danymi przechowywanymi w ADLS. Jeśli grupa nie jest przeznaczona do zarządzania usługami Azure, należy przypisać do tej roli.
6.  Wpisz nazwę grupy.
7.  Kliknij przycisk **OK**.

## <a name="adding-a-user-using-powershell"></a>Dodawanie użytkownika za pomocą programu PowerShell

1. Postępuj zgodnie z instrukcjami w tym przewodniku: [jak instalowanie i konfigurowanie programu Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Pobierz [AdlaJobUser.ps1 Dodaj](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) skrypt programu PowerShell.
3. Uruchom skrypt programu PowerShell. 

Przykład polecenia, aby udzielić dostępu użytkownika do przesyłania zadań, wyświetlić nowe metadane zadania i widoku, który jest stare metadane:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu portalu Azure](data-lake-analytics-get-started-portal.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

