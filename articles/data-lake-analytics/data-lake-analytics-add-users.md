---
title: Dodawanie użytkowników do konta usługi Azure Data Lake Analytics
description: Dowiedz się, jak poprawnie dodać użytkowników do swojego konta usługi Data Lake Analytics
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 0386406f5fc81a007d55bd5358e7a6b333f63b04
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048337"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Dodanie użytkownika w witrynie Azure portal

## <a name="start-the-add-user-wizard"></a>Uruchom Kreator dodawania użytkownika
1. Otwieranie usługi Azure Data Lake Analytics za pośrednictwem https://portal.azure.com.
2. Kliknij przycisk **użytkownika Kreator dodawania**.
3. W **wybierz użytkownika** kroku, Znajdź użytkownika, które chcesz dodać. Kliknij pozycję **Wybierz**.
4. **wybierz rolę** kroku, wybierz **Data Lake Analytics Developer**. Ta rola ma minimalny zestaw uprawnień wymaganych do przesyłania/monitorowania/zarządzania zadań U-SQL. Przypisz do tej roli, jeśli grupa nie jest przeznaczona do zarządzania usługami platformy Azure.
5. W **Wybieranie uprawnień do katalogu** kroku, wybierz wszystkie dodatkowe bazy danych wymaga dostępu do tego użytkownika. Odczyt i zapis w bazie danych master jest wymagana do przesyłania zadań. Gdy skończysz, kliknij przycisk **OK**.
6. W ostatnim kroku o nazwie **Przypisywanie wybranych uprawnień** przeglądanie zmian spowoduje, że kreator. Kliknij przycisk **OK**.


## <a name="configure-acls-for-data-folders"></a>Skonfiguruj listy ACL dla folderów danych
Przyznaj "R-X" lub "RWX", zgodnie z potrzebami, w przypadku folderów zawierających dane wejściowe i dane wyjściowe.


## <a name="optionally-add-the-user-to-the-azure-data-lake-store-role-reader-role"></a>Opcjonalnie można dodać użytkownika do roli usługi Azure Data Lake Store **czytnika** roli.
1.  Znajdź swoje konto usługi Azure Data Lake Store.
2.  Kliknij pozycję **użytkowników**.
3. Kliknij pozycję **Add** (Dodaj).
4.  Wybierz rolę RBAC platformy Azure, aby przypisać tę grupę.
5.  Przypisz do roli czytelnika. Ta rola ma minimalny zestaw uprawnień wymaganych do przeglądania/zarządzanie danymi zapisanymi w usłudze ADLS. Przypisz do tej roli, jeśli grupa nie jest przeznaczona do zarządzania usługami platformy Azure.
6.  Wpisz nazwę grupy.
7.  Kliknij przycisk **OK**.

## <a name="adding-a-user-using-powershell"></a>Dodanie użytkownika przy użyciu programu PowerShell

1. Postępuj zgodnie z instrukcjami w tym przewodniku: [jak zainstalować i skonfigurować program Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Pobierz [AdlaJobUser.ps1 Dodaj](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) skrypt programu PowerShell.
3. Uruchom skrypt programu PowerShell. 

Przykładowe polecenie, aby udzielić użytkownikowi dostępu do przesyłania zadań, wyświetlić nowymi metadanymi zadań i widoku, który stare metadane są:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-get-started-portal.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

