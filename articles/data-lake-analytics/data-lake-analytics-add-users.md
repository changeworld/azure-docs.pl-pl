---
title: Dodawanie użytkowników do konta usługi Azure Data Lake Analytics
description: Dowiedz się, jak poprawnie dodawać użytkowników do konta usługi Data Lake Analytics za pomocą Kreatora dodawania użytkowników i programu Azure PowerShell.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: db35f16e-1565-4873-a851-bd987accdc58
ms.topic: conceptual
ms.date: 05/24/2018
ms.openlocfilehash: 16c503fe2d584d5f8256c65bfc49825b300f6a36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672729"
---
# <a name="adding-a-user-in-the-azure-portal"></a>Dodawanie użytkownika w witrynie Azure Portal

## <a name="start-the-add-user-wizard"></a>Uruchamianie Kreatora dodawania użytkownika
1. Otwórz usługę Azure Data https://portal.azure.comLake Analytics za pośrednictwem pliku .
2. Kliknij pozycję **Kreator dodawania użytkownika**.
3. W kroku **Wybierz użytkownika** znajdź użytkownika, którego chcesz dodać. Kliknij **pozycję Wybierz**.
4. krok **Wybierz rolę,** wybierz **data lake analytics developer**. Ta rola ma minimalny zestaw uprawnień wymaganych do przesyłania/monitorowania/zarządzania zadaniami U-SQL. Przypisz do tej roli, jeśli grupa nie jest przeznaczona do zarządzania usługami platformy Azure.
5. W kroku **Wybierz uprawnienia katalogu** wybierz dodatkowe bazy danych, do których użytkownik będzie potrzebował dostępu. Dostęp do odczytu i zapisu do głównej bazy danych jest wymagany do przesyłania zadań. Gdy wszystko będzie gotowe, kliknij pozycję **OK**.
6. W ostatnim kroku o nazwie **Przypisz wybrane uprawnienia** przejrzyj zmiany wprowadzone przez kreatora. Kliknij przycisk **OK**.


## <a name="configure-acls-for-data-folders"></a>Konfigurowanie list ACL dla folderów danych
W razie potrzeby udziel "R-X" lub "RWX", w folderach zawierających dane wejściowe i dane wyjściowe.


## <a name="optionally-add-the-user-to-the-azure-data-lake-storage-gen1-role-reader-role"></a>Opcjonalnie dodaj użytkownika do roli **czytnika** danych usługi Azure Data Lake Gen1.
1.  Znajdź swoje konto usługi Azure Data Lake Storage Gen1.
2.  Kliknij pozycję **Użytkownicy**.
3. Kliknij przycisk **Dodaj**.
4.  Wybierz rolę RBAC platformy Azure, aby przypisać tę grupę.
5.  Przypisz do roli czytelnika. Ta rola ma minimalny zestaw uprawnień wymaganych do przeglądania/zarządzania danymi przechowywanymi w ADLSGen1. Przypisz do tej roli, jeśli grupa nie jest przeznaczona do zarządzania usługami platformy Azure.
6.  Wpisz nazwę grupy.
7.  Kliknij przycisk **OK**.

## <a name="adding-a-user-using-powershell"></a>Dodawanie użytkownika przy użyciu programu PowerShell

1. Postępuj zgodnie z instrukcjami zawartymi w tym przewodniku: [Jak zainstalować i skonfigurować program Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
2. Pobierz skrypt [programu PowerShell add-adlaJobUser.ps1.](https://github.com/Azure/AzureDataLake/blob/master/Samples/PowerShell/ADLAUsers/Add-AdlaJobUser.ps1)
3. Uruchom skrypt programu PowerShell. 

Przykładowe polecenie, aby dać użytkownikowi dostęp do przesyłania zadań, wyświetlania nowych metadanych zadań i wyświetlania starych metadanych jest:

`Add-AdlaJobUser.ps1 -Account myadlsaccount -EntityToAdd 546e153e-0ecf-417b-ab7f-aa01ce4a7bff -EntityType User -FullReplication`


## <a name="next-steps"></a>Następne kroki

* [Omówienie usługi Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Wprowadzenie do usługi Data Lake Analytics przy użyciu witryny Azure portal](data-lake-analytics-get-started-portal.md)
* [Zarządzanie usługą Azure Data Lake Analytics przy użyciu usługi Azure PowerShell](data-lake-analytics-manage-use-powershell.md)

