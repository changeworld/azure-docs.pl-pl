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
ms.openlocfilehash: 8323c4e1b236444f55dab826d2567491f5f0f736
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629326"
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


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Opcjonalnie można dodać użytkownika do roli usługi Azure Data Lake Storage Gen1 **czytnika** roli.
1.  Znajdź swoje konto usługi Azure Data Lake Storage Gen1.
2.  Kliknij pozycję **użytkowników**.
3. Kliknij pozycję **Add** (Dodaj).
4.  Wybierz rolę RBAC platformy Azure, aby przypisać tę grupę.
5.  Przypisz do roli czytelnika. Ta rola ma minimalny zestaw uprawnień wymaganych do przeglądania/zarządzanie danymi zapisanymi w ADLSGen1. Przypisz do tej roli, jeśli grupa nie jest przeznaczona do zarządzania usługami platformy Azure.
6.  Wpisz nazwę grupy.
7.  Kliknij przycisk **OK**.

## <a name="adding-a-user-using-powershell"></a>Dodanie użytkownika przy użyciu programu PowerShell

1. Postępuj zgodnie z instrukcjami w tym przewodniku: [Jak zainstalować i skonfigurować program Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Pobierz [AdlaJobUser.ps1 Dodaj](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1) skrypt programu PowerShell.
3. Uruchom skrypt programu PowerShell. 

Przykładowe polecenie, aby udzielić użytkownikowi dostępu do przesyłania zadań, wyświetlić nowymi metadanymi zadań i widoku, który stare metadane są:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Kolejne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Rozpoczynanie pracy z usługą Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-get-started-portal.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu programu Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

